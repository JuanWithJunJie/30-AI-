# CLAUDE.md - 项目经验记录

## Lark CLI 使用经验

### 踩坑记录

#### 问题：使用 lark-cli 写入飞书文档/表格失败

**错误做法**：
- 使用 raw API：`POST /open-apis/docx/v1/documents/{id}/blocks` → 返回 404
- 使用 raw API：`POST /open-apis/sheets/v2/spreadsheets/{id}/values` → 返回 404
- 在没有验证高层命令的情况下，直接判定 lark-cli 有 bug

**根本原因**：
- raw API 的方法/路径不对
- 没有使用 lark-cli 提供的高层命令

**正确做法**：
1. **文档操作**：使用 `docs +fetch`、`docs +update` 等高层命令
2. **表格操作**：使用 `sheets +info`、`sheets +read`、`sheets +write`、`sheets +append` 等高层命令
3. **每次请求前加 `LARK_CLI_NO_PROXY=1`** 避免代理干扰

### 正确的 lark-cli 命令

```bash
# 认证状态检查
LARK_CLI_NO_PROXY=1 lark-cli auth status

# 文档读取
LARK_CLI_NO_PROXY=1 lark-cli docs +fetch --doc '文档URL'

# 文档追加写入（支持 @文件名 或直接字符串）
LARK_CLI_NO_PROXY=1 lark-cli docs +update --doc '文档URL' --mode append --markdown @文件.md
LARK_CLI_NO_PROXY=1 lark-cli docs +update --doc '文档URL' --mode append --markdown '直接写入的文本'

# 表格信息
LARK_CLI_NO_PROXY=1 lark-cli sheets +info --url '表格URL'

# 表格读取
LARK_CLI_NO_PROXY=1 lark-cli sheets +read --spreadsheet-token '表格token' --range 'sheet_id!A1:D10'

# 表格写入
LARK_CLI_NO_PROXY=1 lark-cli sheets +write --spreadsheet-token '表格token' --range 'sheet_id!A1' --values '[["列1","列2"],["值1","值2"]]'

# 表格追加
LARK_CLI_NO_PROXY=1 lark-cli sheets +append --spreadsheet-token '表格token' --range 'sheet_id!A1' --values '[["值1","值2"]]'
```

### 排查顺序

1. 先用 `lark-cli auth status` 确认 token 和 scopes 正常
2. 用高层命令（如 `docs +fetch`、`sheets +info`）验证目标可读
3. 使用高层命令进行写入，不要先用 raw API
4. 写入前用 `--dry-run` 验证命令格式
5. 每次请求加 `LARK_CLI_NO_PROXY=1`

### 不要重复的错误

1. ❌ 不要优先怀疑 lark-cli 的 POST 有 bug
2. ❌ 不要优先使用 raw OpenAPI 路径（除非高层命令确实不可用）
3. ❌ 不要忘记加 `LARK_CLI_NO_PROXY=1`
4. ❌ 不要在不验证读取的情况下直接尝试写入
