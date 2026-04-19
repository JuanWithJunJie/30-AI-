# Lark CLI 问题排查报告

## 问题概述

使用 Lark CLI 尝试向飞书云文档写入内容时，部分 API 调用失败。

## 环境信息

- lark-cli 版本：1.0.14
- Node.js：已安装
- 操作系统：macOS
- 代理：HTTPS_PROXY=http://127.0.0.1:7890

## 已验证正常的 API

| API | 方法 | 状态 |
|-----|------|------|
| GET /docx/v1/documents/{id}/blocks | GET | ✅ 正常 |
| POST /docx/v1/documents | POST | ✅ 正常（创建文档） |
| POST /sheets/v3/spreadsheets | POST | ✅ 正常（创建表格） |
| GET /sheets/v3/spreadsheets/{id}/sheets/query | GET | ✅ 正常 |
| GET /drive/v1/files | GET | ✅ 正常 |
| POST /calendar/v4/calendars | POST | ✅ 正常 |

## 失败的 API

### 1. POST /docx/v1/documents/{id}/blocks（核心问题）

**用途**：向云文档添加文本块

**请求**：
```bash
lark-cli api POST "/open-apis/docx/v1/documents/{document_id}/blocks" \
  --data '{"children":[{"block_type":2,"text":{"elements":[{"type":"text_run","text_run":{"content":"Hello","text_element_style":{}}}]}}],"index":0}'
```

**响应**：
```
HTTP 404: 404 page not found
```

**dry-run 输出**（看起来正确）：
```json
{
  "method": "POST",
  "url": "/open-apis/docx/v1/documents/{document_id}/blocks",
  "body": {
    "children": [{"block_type": 2, "text": {...}}],
    "index": 0
  }
}
```

**观察**：
- GET blocks 正常返回数据
- POST blocks 返回 404
- 使用不同文档 ID 测试，结果相同
- 怀疑：lark-cli 在发送 POST 请求时 URL 或 Header 有问题

### 2. POST /sheets/v2/spreadsheets/{id}/values

**用途**：向电子表格写入数据

**请求**：
```bash
lark-cli api POST "/open-apis/sheets/v2/spreadsheets/{spreadsheet_id}/values" \
  --data '{"valueRanges":[{"range":"A1","values":[["Test"]]}]}'
```

**响应**：
```
HTTP 404: 404 page not found
```

**观察**：
- 创建电子表格成功
- 查询表格信息成功
- 写入数据返回 404

## 调试尝试

1. ✅ 更新到最新版本 1.0.14
2. ✅ 尝试设置 `LARK_CLI_NO_PROXY=1` 禁用代理 - 无效
3. ✅ 清除所有代理环境变量 - 无效
4. ✅ 使用 --dry-run 检查请求格式 - 格式正确
5. ✅ 使用不同文档 ID 测试 - 全部返回 404
6. ✅ 使用 --as bot 指定身份 - 同样失败
7. ❌ 尝试直接使用 curl 调用飞书 API - Token 无效（需要 OAuth）

## 推测原因

1. **lark-cli bug**：某些 POST API 端点在发送请求时路径或配置有误
2. **代理问题**：虽然设置了 `LARK_CLI_NO_PROXY`，但代理可能仍在干扰某些请求
3. **API 版本问题**：可能需要使用不同版本的 API（如 v1 vs v2）

## 测试文件

- 本地汇总文件：`AI案例分类汇总.md`
- 目标飞书文档：https://my.feishu.cn/docx/LJzxdbPZZo9j9uxUU4IcABmWnNb
- 测试用电子表格：https://my.feishu.cn/sheets/QduPslauih4t83tACvdcZgJtnug

## 建议

1. 检查 lark-cli 的源码，看 POST /docx/v1/documents/{id}/blocks 请求是如何发送的
2. 验证飞书 Open Platform 的实际 API 端点是否与 lark-cli 使用的一致
3. 考虑是否有代理干扰的情况需要特殊处理
