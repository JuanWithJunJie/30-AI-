# Cloud Code 使用 Lark CLI 的排查与执行指引

## 先说结论

这次问题的高概率根因，不是 `lark-cli` 整体不能发 `POST`，而是之前调用的**写接口路径/方法不对**。

已确认：

1. `POST /open-apis/sheets/v2/spreadsheets/{spreadsheet_id}/values` 这条写法不对。
   正确写入是：
   - 覆盖写入：`PUT /open-apis/sheets/v2/spreadsheets/{spreadsheetToken}/values`
   - 追加写入：`POST /open-apis/sheets/v2/spreadsheets/{spreadsheetToken}/values_append`

2. 云文档写入不建议继续手拼 `POST /open-apis/docx/v1/documents/{id}/blocks`。
   `lark-cli` 官方高层命令 `docs +update` 实际走的是 `mcp.feishu.cn` 上的 `update-doc` 工具，而不是你之前测试的那条 raw OpenAPI。

3. 当前环境里的认证、权限和 CLI 本身都是可用的。

## 我本机已经实测通过的结果

### 文档链路

我用下面的命令成功创建、更新、读取了一个临时飞书文档：

```bash
LARK_CLI_NO_PROXY=1 lark-cli docs +create \
  --title 'Codex 临时排查文档 2026-04-19 1' \
  --markdown '# 临时排查\n\n初始化内容'

LARK_CLI_NO_PROXY=1 lark-cli docs +update \
  --doc BtH2dBfXuo8yJ1xkesvczZN3nBb \
  --mode append \
  --markdown $'\n\n## 验证追加\n这是一条来自 Codex 的追加测试。'

LARK_CLI_NO_PROXY=1 lark-cli docs +fetch \
  --doc BtH2dBfXuo8yJ1xkesvczZN3nBb
```

`docs +update` 返回成功，`docs +fetch` 能读到追加后的 markdown。

### 表格链路

我用下面的命令成功创建、写入、读取了一个临时电子表格：

```bash
LARK_CLI_NO_PROXY=1 lark-cli sheets +create \
  --title 'Codex 临时排查表格 2026-04-19 1'

LARK_CLI_NO_PROXY=1 lark-cli sheets +info \
  --spreadsheet-token JiuPsuo2Bhek77tuLMkcHb1MnYg

LARK_CLI_NO_PROXY=1 lark-cli sheets +write \
  --spreadsheet-token JiuPsuo2Bhek77tuLMkcHb1MnYg \
  --range '100f87!A1:B2' \
  --values '[[\"验证\",\"通过\"],[\"writer\",\"lark-cli sheets +write\"]]'

LARK_CLI_NO_PROXY=1 lark-cli sheets +read \
  --spreadsheet-token JiuPsuo2Bhek77tuLMkcHb1MnYg \
  --range '100f87!A1:B2'
```

`+write` 返回 `updatedCells: 4`，`+read` 能读到刚写入的数据。

## 对 Cloud Code 的明确要求

### 1. 不要先怀疑 `lark-cli` 的 POST 都坏了

之前的 404 更像是调用方式不对：

- 表格写入：把 `PUT` 写成了 `POST`
- 文档写入：使用了不该优先使用的 raw block 接口

### 2. 优先使用 Lark CLI 的高层命令

按项目要求，所有 Feishu/Lark 云文档操作优先用 `Lark CLI`，不要先退回浏览器手工操作。

这次建议直接使用：

- 文档读取：`lark-cli docs +fetch`
- 文档写入：`lark-cli docs +update`
- 表格读取：`lark-cli sheets +read`
- 表格写入：`lark-cli sheets +write`
- 表格追加：`lark-cli sheets +append`

### 3. 每次请求先临时禁用代理

当前环境里存在：

```bash
HTTPS_PROXY=http://127.0.0.1:7890
```

虽然它不一定是主因，但为了避免干扰，执行命令时统一加：

```bash
LARK_CLI_NO_PROXY=1
```

## 推荐的排查/执行顺序

### 第一步：确认认证和权限

```bash
LARK_CLI_NO_PROXY=1 lark-cli auth status
```

至少确认：

- `tokenStatus` 是 `valid`
- scope 里包含：
  - `docx:document:write_only`
  - `docx:document:readonly`
  - `sheets:spreadsheet:write_only`
  - `sheets:spreadsheet:read`

### 第二步：先验证读取链路

#### 文档读取

```bash
LARK_CLI_NO_PROXY=1 lark-cli docs +fetch \
  --doc 'https://my.feishu.cn/docx/LJzxdbPZZo9j9uxUU4IcABmWnNb'
```

#### 表格信息

```bash
LARK_CLI_NO_PROXY=1 lark-cli sheets +info \
  --url 'https://my.feishu.cn/sheets/QduPslauih4t83tACvdcZgJtnug'
```

从返回值里拿到 `sheet_id`，例如 `3c7f93`。

### 第三步：不要再用错误的 raw API，改用高层命令

#### 给云文档追加内容

```bash
LARK_CLI_NO_PROXY=1 lark-cli docs +update \
  --doc 'https://my.feishu.cn/docx/LJzxdbPZZo9j9uxUU4IcABmWnNb' \
  --mode append \
  --markdown @AI案例分类汇总.md
```

如果只想先写一小段测试文本：

```bash
LARK_CLI_NO_PROXY=1 lark-cli docs +update \
  --doc 'https://my.feishu.cn/docx/LJzxdbPZZo9j9uxUU4IcABmWnNb' \
  --mode append \
  --markdown '测试写入：Cloud Code 已接管'
```

#### 给电子表格写入数据

先拿到表格的 `sheet_id`，然后：

```bash
LARK_CLI_NO_PROXY=1 lark-cli sheets +write \
  --spreadsheet-token 'QduPslauih4t83tACvdcZgJtnug' \
  --range '3c7f93!A1:B2' \
  --values '[[\"案例\",\"分类\"],[\"测试\",\"成功\"]]'
```

写完立刻读回：

```bash
LARK_CLI_NO_PROXY=1 lark-cli sheets +read \
  --spreadsheet-token 'QduPslauih4t83tACvdcZgJtnug' \
  --range '3c7f93!A1:B2'
```

### 第四步：如果一定要看底层请求，用 `--dry-run` 验证

#### 表格写入的底层请求应该长这样

```bash
LARK_CLI_NO_PROXY=1 lark-cli sheets +write \
  --spreadsheet-token 'QduPslauih4t83tACvdcZgJtnug' \
  --range '3c7f93!A1' \
  --values '[[\"Test\"]]' \
  --dry-run
```

我本机看到的是：

```json
{
  "api": [
    {
      "method": "PUT",
      "url": "/open-apis/sheets/v2/spreadsheets/QduPslauih4t83tACvdcZgJtnug/values"
    }
  ]
}
```

#### 文档更新的底层请求应该长这样

```bash
LARK_CLI_NO_PROXY=1 lark-cli docs +update \
  --doc 'https://my.feishu.cn/docx/LJzxdbPZZo9j9uxUU4IcABmWnNb' \
  --mode append \
  --markdown 'Hello' \
  --dry-run
```

我本机看到的是：

```json
{
  "api": [
    {
      "method": "POST",
      "url": "https://mcp.feishu.cn/mcp"
    }
  ],
  "mcp_tool": "update-doc"
}
```

这说明 Cloud Code 不该再坚持用 `POST /open-apis/docx/v1/documents/{id}/blocks` 作为主要写入方案。

## 不要再走的错误方向

下面这些方向现在都不应作为首选：

1. 继续把 `POST /open-apis/sheets/v2/spreadsheets/{id}/values` 当成写入接口
2. 继续把 `POST /open-apis/docx/v1/documents/{id}/blocks` 当成首选文档写入接口
3. 在没有先跑 `docs +update` / `sheets +write` 的情况下，就直接判定是 `lark-cli bug`
4. 未加 `LARK_CLI_NO_PROXY=1` 就对代理问题做过度推断

## 如果高层命令仍失败，Cloud Code 应继续收集这些信息

如果 `docs +update` 或 `sheets +write` 仍失败，请让 Cloud Code 输出下面这些信息，再继续判断：

1. 完整命令
2. 完整 stdout/stderr
3. `lark-cli auth status`
4. 对应命令的 `--dry-run` 输出
5. `docs +fetch` 或 `sheets +read/+info` 是否成功
6. 是否加了 `LARK_CLI_NO_PROXY=1`
7. 失败的是高层命令，还是手写 raw API

## 可直接贴给 Cloud Code 的任务说明

你现在不要优先怀疑 lark-cli 的 POST 有 bug，也不要先退回浏览器手工操作。  
请严格按下面顺序执行：

1. 用 `LARK_CLI_NO_PROXY=1 lark-cli auth status` 确认 token 和 scopes 正常。
2. 用 `lark-cli docs +fetch` 验证目标文档可读。
3. 用 `lark-cli sheets +info` 验证目标表格可读，并拿到 `sheet_id`。
4. 文档写入必须优先尝试 `lark-cli docs +update`，不要先用 `POST /open-apis/docx/v1/documents/{id}/blocks`。
5. 表格写入必须优先尝试 `lark-cli sheets +write` 或 `+append`，不要再用错误的 `POST /open-apis/sheets/v2/spreadsheets/{id}/values`。
6. 每次实际写入前先跑一遍 `--dry-run`，确认底层 method/path 正确。
7. 如果失败，请把完整命令、完整报错、`auth status`、`--dry-run` 输出一起保留下来，再继续分析。

目标不是证明 raw API 猜测是否成立，而是先把“通过 Lark CLI 成功写入飞书文档/表格”这件事完成。
