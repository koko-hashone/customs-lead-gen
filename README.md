# customs-lead-gen

外贸海关数据获客 AI Skill — 基于海关提单数据自动发现、评估、联系海外买家的完整工作流。

## 项目简介

这是一个为外贸业务员设计的 AI 自动化获客工具，通过海关提单数据、浏览器自动化和 MCP 服务器，实现从产品关键词到联系到采购决策人的全流程自动化。

### 核心能力

- **HS 编码自动查询**：输入产品名称，自动匹配 HS 编码
- **市场分析**：Trade Map 宏观数据 + ImportYeti 提单数据
- **采购商深度开发**：逐页串行、深度优先处理
- **决策人搜索**：enrich MCP + LinkedIn 搜索，同时获取公司邮箱和决策人
- **双通道触达**：邮件 + WhatsApp 并行，互不替代
- **评级入库**：A/B/C 自动评级，CRM 入库

### 支持的产品品类

| 品类 | HS 编码示例 |
|------|-----------|
| 光伏支架 | 73089000 |
| LED 灯具 | 940542 / 940561 |
| 不锈钢水杯 | 961700 / 732393 |
| 锂电池 | 850760 |
| 手机壳 | 392690 |

---

## 安装方式

### 方式 1：作为 MiMoCode Skill 安装（推荐）

```bash
# 克隆到 skills 目录
git clone https://github.com/koko-hashone/customs-lead-gen.git ~/.agents/skills/customs-lead-gen
```

安装后，在 MiMoCode 中直接输入：
```
用海关数据找客户
```
或指定产品：
```
光伏支架的美国买家
LED灯具的采购联系人
```

### 方式 2：作为独立项目使用

```bash
git clone https://github.com/koko-hashone/customs-lead-gen.git
cd customs-lead-gen
```

---

## MCP 服务器配置

本 skill 依赖以下 MCP 服务器，需要先配置：

### 必装（免费）

| MCP | 用途 | 安装命令 |
|-----|------|---------|
| **enrich** | 域名/公司名 → 联系人邮箱电话 | `claude mcp add enrich https://www.tradego.ai/mcp --transport http` |
| **playwright** | 浏览器自动化，抓取 JS 渲染页面 | `claude mcp add playwright -- npx playwright-mcp` |

### 可选（免费）

| MCP | 用途 | 安装命令 |
|-----|------|---------|
| **linkedin** | LinkedIn 搜索采购决策人 | `claude mcp add linkedin -- uvx mcp-server-linkedin@latest` |

### 一键配置

```bash
# enrich（无需 API Key）
claude mcp add enrich https://www.tradego.ai/mcp --transport http

# Playwright（浏览器自动化）
claude mcp add playwright -- npx playwright-mcp

# LinkedIn（搜索决策人，需要登录）
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

### 验证安装

```bash
claude mcp list
```

应该看到：
```
enrich: https://www.tradego.ai/mcp - ✓ Connected
playwright: npx playwright-mcp - ✓ Connected
linkedin: uvx mcp-server-linkedin@latest - ✓ Connected
```

---

## 使用方法

### 基本用法

在 MiMoCode 中直接输入自然语言指令：

```
用海关数据找客户
```

或指定产品和目标市场：

```
光伏支架的美国买家
LED灯具的采购联系人
不锈钢水杯的美国进口商
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

### 各步骤详解

| 步骤 | 做什么 | 用什么工具 |
|------|--------|-----------|
| **第零步** | 查 HS 编码 | AI 直接查 / 网页工具 |
| **第一步** | 市场分析（哪些国家进口量大） | Playwright → Trade Map + ImportYeti |
| **第二步 Step A** | 抓取一页采购商列表 | Playwright → ImportYeti |
| **第二步 Step B** | 逐个处理每个采购商 |  |
| ↳ 2.1 背调 | 抓取公司详情页 | Playwright → ImportYeti |
| ↳ 2.2 评级 | A/B/C 打级 | 逻辑判断 |
| ↳ 2.3 找决策人 | enrich + LinkedIn 搜索 | enrich MCP + LinkedIn MCP |
| ↳ 2.4 入库 | CRM 创建客户/联系人 | CRM MCP |
| ↳ 2.5 触达 | 邮件 + WhatsApp 并行 | 邮件工具 + WhatsApp MCP |
| **结束** | 总报告 | 汇总统计 |

---

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

---

## 工具优先级

| 优先级 | 工具 | 用途 |
|--------|------|------|
| **1** | **Playwright MCP** | 抓取 JS 渲染页面（ImportYeti、Trade Map、Google） |
| **2** | **enrich MCP** | 域名/公司名 → 联系人邮箱电话 |
| **3** | **LinkedIn MCP** | 搜索采购决策人个人信息 |

---

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

---

## 示例输出

### 搜索光伏支架美国买家

```
产品：光伏支架
HS 编码：73089000
目标市场：美国

采购商列表：
1. Acuity Brands - 16,610 次海运 - info@acuitybrands.com
2. Unirac - 1,713 次海运 - info@unirac.com

采购决策人（LinkedIn）：
- Vernie N. - VP, Sourcing @ Acuity Brands
- Lauren Nepsa - Director of Sourcing @ Acuity Brands
- Derek Johnson - Purchasing Manager @ Satco Products
```

### 搜索 LED 灯具采购联系人

```
产品：LED 灯具
HS 编码：940542 / 940561
目标市场：美国

采购商列表：
1. Acuity Brands - 16,610 次海运 - info@acuitybrands.com
2. Satco Products - 11,729 次海运 - info@satcoproducts.com
3. RAB Lighting - 7,918 次海运 - info@rablighting.com

采购决策人（LinkedIn）：
- Vernie N. - VP, Sourcing @ Acuity Brands
- Lauren Nepsa - Director of Sourcing @ Acuity Brands
- Juan Carlos Flores - Director of Sourcing (Lighting) @ Acuity Brands
- Derek Johnson - Purchasing Manager @ Satco Products
- Deborah Wescott - Supply Chain Manager @ RAB Lighting
```

---

## 常见问题

### Q: enrich MCP 有额度限制吗？
A: 有，免费额度 10 次/月。接近限额时会提醒，额度用完可等待下月刷新或注册新账号。

### Q: ImportYeti 覆盖哪些国家？
A: 仅覆盖美国海运进口数据。

### Q: LinkedIn MCP 需要登录吗？
A: 需要。首次使用会打开浏览器要求登录 LinkedIn，登录后自动保存 session。

### Q: Playwright MCP 和 webfetch 有什么区别？
A: Playwright 可以执行 JavaScript，能抓取 ImportYeti、Trade Map 等 JS 渲染页面；webfetch 只能抓取静态页面。

### Q: 如何找到采购决策人？
A: 同时使用两个渠道：
1. **enrich MCP** → 获取公司通用邮箱（purchasing@, info@）
2. **LinkedIn 搜索** → 获取采购决策人姓名、职位、个人页

---

## 许可证

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/your-feature`)
3. 提交更改 (`git commit -m 'Add some feature'`)
4. 推送到分支 (`git push origin feature/your-feature`)
5. 创建 Pull Request
- [Trade Map](https://www.trademap.org) - 全球贸易统计
