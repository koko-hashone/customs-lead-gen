# MCP 服务器配置详情

## 1. Playwright MCP（免费，必装）⭐

**用途**：浏览器自动化，抓取 JS 渲染页面（ImportYeti、Trade Map、Google、公司官网）

**安装**：
```bash
claude mcp add playwright -- npx playwright-mcp
```

**工具列表**：
| 工具 | 参数 | 说明 |
|------|------|------|
| `playwright_navigate` | `url`: 目标网址 | 打开页面 |
| `playwright_wait` | `time`: 等待秒数 | 等待页面加载 |
| `playwright_get_content` | 无 | 提取页面 HTML 内容 |
| `playwright_click` | `selector`: CSS 选择器 | 点击元素 |
| `playwright_fill` | `selector`, `value` | 填写表单 |
| `playwright_screenshot` | 无 | 截图 |

**抓取 ImportYeti 示例**：
```bash
playwright_navigate(url="https://www.importyeti.com/search?q=LED+lighting&type=company")
playwright_wait(time=5)
playwright_get_content()
```

---

## 2. LinkedIn MCP（免费，推荐安装）⭐⭐⭐

**用途**：搜索 LinkedIn 决策人、获取公司信息、发送连接请求

**安装**：
```bash
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

**工具列表**：
| 工具 | 说明 |
|------|------|
| `search_people` | 搜索 LinkedIn 用户（关键词/公司/职位/地区） |
| `get_person_profile` | 获取个人资料（经历/教育/技能） |
| `get_company_profile` | 获取公司信息 |
| `get_company_employees` | 列出公司员工 |
| `search_companies` | 搜索公司 |
| `connect_with_person` | 发送连接请求 |
| `send_message` | 发送消息 |
| `get_inbox` | 获取收件箱 |

**使用示例**：
```bash
# 搜索采购决策人
search_people(keywords="Purchasing Manager", company="Acuity Brands", location="United States")

# 获取决策人资料
get_person_profile(url="linkedin.com/in/xxx")

# 发送连接请求
connect_with_person(url="linkedin.com/in/xxx", note="Hi, I'm from a LED lighting manufacturer...")
```

**注意事项**：
- 首次使用需要登录 LinkedIn（会打开浏览器）
- 自动导入已登录的浏览器 session
- 使用时需遵守 LinkedIn 用户协议

---

## 3. enrich MCP（免费，必装）

**用途**：域名/公司名 → 公司联系人（邮箱、电话、社交）

**安装**：
```bash
claude mcp add enrich https://www.tradego.ai/mcp --transport http
```

**工具列表**：
| 工具 | 参数 | 返回 |
|------|------|------|
| `enrich_company` | `domain`: 域名 | 公司名、国家、联系人邮箱、电话、社交链接 |
| `enrich_company_by_name` | `name`: 公司名 | 同上 |

**调用示例**：
```bash
# Step 1: 初始化
curl -s -X POST https://www.tradego.ai/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"enrich","version":"1.0"}}}'

# Step 2: 获取 session-id（从响应头）

# Step 3: 发送 initialized 通知
curl -s -X POST https://www.tradego.ai/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -H "mcp-session-id: {session_id}" \
  -d '{"jsonrpc":"2.0","method":"notifications/initialized"}'

# Step 4: 调用 enrich_company
curl -s -X POST https://www.tradego.ai/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -H "mcp-session-id: {session_id}" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"enrich_company","arguments":{"domain":"example.com"}}}'
```

---

## 完整 .mcp.json 示例

```json
{
  "mcpServers": {
    "enrich": {
      "type": "http",
      "url": "https://www.tradego.ai/mcp"
    },
    "playwright": {
      "command": "npx",
      "args": ["playwright-mcp"]
    },
    "linkedin": {
      "command": "uvx",
      "args": ["mcp-server-linkedin@latest"]
    }
  }
}
```

## Claude Code 一键配置

```bash
# Playwright MCP（浏览器自动化）
claude mcp add playwright -- npx playwright-mcp

# enrich MCP（联系人查找）
claude mcp add enrich https://www.tradego.ai/mcp --transport http

# LinkedIn MCP（搜索决策人，推荐）
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

## 付费工具（暂未配置）

| 工具 | 用途 | 费用 |
|------|------|------|
| Apollo.io MCP | 2.7亿联系人搜索 | 付费 |
| mcp-crm | CRM 管理 | 各平台 API |
| WBMCP | WhatsApp 消息 | WhatsApp API |
| hs-code-classifier | HS 编码自动分类 | 需 API Key |
| outscraper | Google Maps 数据 | 付费 |
