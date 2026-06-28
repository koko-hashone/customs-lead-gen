# customs-lead-gen

外贸海关数据获客 AI Skill — 基于海关提单数据自动发现、评估、联系海外买家的完整工作流。

## 功能特性

- **HS 编码自动查询**：输入产品名称，自动匹配 HS 编码
- **市场分析**：Trade Map 宏观数据 + ImportYeti 提单数据
- **采购商深度开发**：逐页串行、深度优先处理
- **决策人搜索**：enrich MCP + LinkedIn 搜索，同时获取公司邮箱和决策人
- **双通道触达**：邮件 + WhatsApp 并行，互不替代
- **评级入库**：A/B/C 自动评级，CRM 入库

## 安装方式

### 方式 1：作为 MiMoCode Skill 安装

```bash
# 复制到 skills 目录
cp -r customs-lead-gen ~/.agents/skills/

# 或者克隆
git clone https://github.com/your-username/customs-lead-gen.git ~/.agents/skills/customs-lead-gen
```

### 方式 2：作为独立项目使用

```bash
git clone https://github.com/your-username/customs-lead-gen.git
cd customs-lead-gen
```

## MCP 服务器配置

### 必装（免费）

| MCP | 安装命令 | 用途 |
|-----|---------|------|
| **enrich** | `claude mcp add enrich https://www.tradego.ai/mcp --transport http` | 域名/公司名 → 联系人邮箱电话 |
| **playwright** | `claude mcp add playwright -- npx playwright-mcp` | 浏览器自动化，抓取 JS 页面 |

### 可选（免费）

| MCP | 安装命令 | 用途 |
|-----|---------|------|
| **linkedin** | `claude mcp add linkedin -- uvx mcp-server-linkedin@latest` | LinkedIn 搜索决策人 |

### 一键配置

```bash
# enrich（无需 API Key）
claude mcp add enrich https://www.tradego.ai/mcp --transport http

# Playwright（浏览器自动化）
claude mcp add playwright -- npx playwright-mcp

# LinkedIn（搜索决策人，需要登录）
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

## 使用方法

### 基本用法

在 MiMoCode 中直接输入：

```
用海关数据找客户
```

或指定产品：

```
光伏支架的美国买家
LED灯具的采购联系人
```

### 完整工作流

```
输入产品关键词/HS编码
        │
        ▼
┌─ 第零步：HS编码查询 ─┐
│  告诉我产品名称       │
│  → 我帮你查 HS 编码   │
└────────┬──────────────┘
         │
         ▼
┌─ 第一步：市场分析 ──────┐
│  Playwright 抓取        │
│  Trade Map + ImportYeti │
│  → 选定优先目的国       │
└────────┬───────────────┘
         │
         ▼
┌─ 第二步：采购商深度开发 ─┐
│  ImportYeti 采购商列表  │
│  enrich 查联系人        │
│  LinkedIn 找决策人      │
│  评级 A/B/C             │
│  双通道触达             │
└────────┬───────────────┘
         │
         ▼
┌─ 结束：总报告 ─┐
└────────────────┘
```

## 文件结构

```
customs-lead-gen/
├── SKILL.md                    # 主文档（工作流 + MCP 配置）
├── README.md                   # 本文件
└── references/
    ├── mcp-setup.md            # MCP 服务器配置详情
    ├── email-templates.md      # 邮件模板库
    └── importyeti-guide.md     # ImportYeti 使用指南
```

## 工具优先级

| 优先级 | 工具 | 用途 |
|--------|------|------|
| **1** | **Playwright MCP** | 抓取 JS 渲染页面（ImportYeti、Trade Map、Google） |
| **2** | **enrich MCP** | 域名/公司名 → 联系人邮箱电话 |
| **3** | **LinkedIn MCP** | 搜索采购决策人个人信息 |

## 免费工具矩阵

| 功能 | 免费方案 |
|------|---------|
| HS 编码查询 | AI 直接查询 / 网页工具 |
| 海关提单 | ImportYeti（仅美国海运） |
| 宏观贸易统计 | Trade Map (ITC) |
| 公司联系人 | enrich-mcp-plugin |
| 决策人搜索 | LinkedIn 搜索 |
| 邮件发送 | Gmail + Mailmeteor |
| Google 搜索 | 内置 webfetch |

## 示例

### 搜索光伏支架美国买家

```
光伏支架的美国买家
```

**输出**：
- HS 编码：73089000
- 目标市场：美国
- 采购商：Acuity Brands (16,610 次海运)
- 决策人：Vernie N. (VP, Sourcing)
- 采购邮箱：info@acuitybrands.com

### 搜索 LED 灯具采购联系人

```
LED灯具的美国采购联系人
```

**输出**：
- HS 编码：940542 / 940561
- 采购商：Acuity Brands, Satco, RAB Lighting
- 决策人：16 人（通过 LinkedIn 找到）
- 采购邮箱：info@acuitybrands.com, info@satcoproducts.com

## 注意事项

1. **enrich MCP 有额度限制**：10 次/月，接近限额时会提醒
2. **ImportYeti 仅覆盖美国海运数据**
3. **LinkedIn MCP 需要登录**：首次使用会打开浏览器要求登录
4. **Playwright MCP** 用于抓取 JS 渲染页面，比 webfetch 更稳定

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request！
