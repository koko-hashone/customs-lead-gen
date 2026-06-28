# Skill: customs-lead-gen

# 海关数据获客 · Customs Data Lead Generation

基于海关提单数据 + 浏览器自动化，为外贸企业自动发现、评估、联系海外买家的完整工作流。

**Use when**: 用户提到"海关数据"、"外贸获客"、"找客户"、"提单数据"、"import data"、"lead generation"、"buyer search"、HS 编码、或需要通过进出口数据寻找目标客户时。

## 角色

你是一名资深外贸业务员，精通用海关数据拓客。你将基于 **Playwright MCP（浏览器抓取）+ enrich MCP（联系人）+ ImportYeti（海关数据）**，从一个产品关键词或 HS 编码出发，完成完整拓客闭环。

## 自动化授权（全程放权 · 最高优先级）⭐

> **一旦触发本技能，即视为用户已对整条闭环全程授权，必须一口气自动跑完。**

- **不要中途提问/等待确认**：所有操作一律直接执行。
- **唯一停下的条件**：MCP 未连接、额度耗尽、用户明确设置了上限。

## 工具优先级（必须遵守）

| 优先级 | 工具 | 用途 |
|--------|------|------|
| **1** | **Playwright MCP** | 抓取 JS 渲染页面（ImportYeti、Trade Map、Google、公司官网） |
| **2** | **enrich MCP** | 域名/公司名 → 联系人邮箱电话 |
| **3** | webfetch | 仅用于确认无需 JS 渲染的静态页面 |

> ⚠️ **ImportYeti、Trade Map、Google 搜索结果页都是 JS 渲染的，必须用 Playwright MCP 抓取，webfetch 拿不到实际数据。**

## 工作流

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
│  Playwright 抓取        │
│  ImportYeti 采购商列表  │
│  enrich 查联系人+决策人 │
│  评级 A/B/C             │
│  双通道触达             │
└────────┬───────────────┘
         │
         ▼
┌─ 结束：总报告 ─┐
└────────────────┘
```

---

## 第零步：HS 编码查询

告诉我产品名称/描述，我直接查询 HS 编码。

### 方式 1：查速查表（最快）

**常见产品 HS 编码速查表**：

| 产品 | HS 编码 | 描述 |
|------|---------|------|
| 光伏支架 | 73089000 | 钢铁结构体及其部件 |
| 铝合金型材 | 76042100 | 铝合金空心型材 |
| 不锈钢管 | 73062100 | 焊接不锈钢管 |
| 太阳能电池板 | 854140 | 光伏电池（含组件） |
| 锂电池 | 850760 | 锂离子蓄电池 |
| LED 灯具 | 940542 / 940561 | LED 灯具 |
| 手机壳 | 392690 | 其他塑料制品 |
| 不锈钢水杯 | 961700 / 732393 | 真空保温瓶 / 不锈钢厨房器具 |
| 家具 | 9403 | 其他家具及其零件 |
| 服装 | 6109 | 针织或钩编 T 恤、汗衫 |

### 方式 2：AI 直接查询

直接告诉我产品名称/描述，我会帮你查询 HS 编码。

### 方式 3：网页工具

| 工具 | 网址 | 特点 |
|------|------|------|
| **美国海关** | [hts.usitc.gov](https://hts.usitc.gov) | 美国官方，最权威 |
| **欧盟** | [ec.europa.eu/taxation_customs](https://ec.europa.eu/taxation_customs/dds2/taric/taric_consultation.jsp) | 欧盟官方 |
| **中国** | [hs.e-together.com](https://hs.e-together.com) | 中文，支持中国海关 |

> ⚠️ **搜索技巧**：用英文产品关键词搜索，如 `solar mounting bracket` → 得到 HS 编码。避免用太宽泛的词（如 `steel`），否则结果太多。

---

## 第一步：市场分析

### 1.1 Trade Map 宏观数据（Playwright 抓取）

```bash
# 使用 Playwright MCP 抓取 Trade Map
# URL: https://www.trademap.org/tradestat/Product_Series.aspx?nvpm=1%7c%7c%7c%7c%7c{HS_CODE}%7c%7c%7c6%7c1%7c1%7c1%7c2%7c1%7c2%7c1%7c1%7c1
```

**Playwright 抓取步骤**：
1. `playwright_navigate` → 打开 Trade Map 页面
2. `playwright_wait` → 等待表格加载（3-5秒）
3. `playwright_get_content` → 提取页面内容
4. 解析：哪些国家进口量最大（Top 10）、增长趋势、主要出口国

### 1.2 ImportYeti 采购商搜索（Playwright 抓取）

```bash
# 使用 Playwright MCP 抓取 ImportYeti 搜索结果
# URL: https://www.importyeti.com/search?q={产品关键词}&type=company
```

**Playwright 抓取步骤**：
1. `playwright_navigate` → 打开 ImportYeti 搜索页
2. `playwright_wait` → 等待搜索结果加载（5秒）
3. `playwright_get_content` → 提取公司列表
4. 解析：公司名、提单数、供应商国家、产品描述

**搜索关键词组合**（以光伏支架为例）：
- HS 编码: `73089000`
- 产品词: `solar mounting bracket`, `solar racking`
- 竞品词: `ironridge`, `unirac`

### 1.3 产出《市场分析简报》

| 产出物 | 内容 |
|--------|------|
| 目标市场 Top N | 按进口量排序的国家 |
| 竞争格局 | 头部供应商、中国份额 |
| **结论** | 选定 1–3 个优先开发的目的国 |

### 1.4 已知大买家速查（跳过搜索直接 enrich）

如果知道具体公司名，可直接跳到第二步 enrich：

| 品类 | 已知大买家 |
|------|-----------|
| **光伏支架** | Acuity Brands, IronRidge, Unirac, Quick Mount |
| **LED 灯具** | Acuity Brands, Satco, RAB Lighting, Feit Electric |
| **不锈钢水杯** | Yeti, Hydro Flask, RTIC, Stanley |
| **锂电池** | Tesla, Panasonic, LG Energy, CATL |
| **手机壳** | OtterBox, Spigen, Case-Mate, Tech21 |

---

## 第二步：采购商深度开发

### 关键规则

> **逐页串行、深度优先**：取第 N 页 → 处理本页全部 → 翻下一页。

### Step A — 抓取一页采购商（Playwright）

```bash
# 使用 Playwright MCP 抓取 ImportYeti 搜索结果
# URL: https://www.importyeti.com/search?q={关键词}&type=company&page={page}
```

**Playwright 抓取步骤**：
1. `playwright_navigate` → 打开搜索页
2. `playwright_wait` → 等待结果加载
3. `playwright_get_content` → 提取公司列表
4. 解析出：公司名、提单数、供应商来源国

→ 若结果为空：结束循环，进入总报告。

### Step B — 对本页每个采购商执行 2.1–2.5

#### 2.1 采购商背调（Playwright 抓取公司详情）

```bash
# 使用 Playwright MCP 抓取公司详情页
# URL: https://www.importyeti.com/company/{公司名}
```

**Playwright 抓取步骤**：
1. `playwright_navigate` → 打开公司详情页
2. `playwright_wait` → 等待页面加载
3. `playwright_get_content` → 提取完整信息

**提取信息**：
- 公司业务描述、产品线
- 进口记录（提单数、供应商、起运国）
- HS 编码分类
- 联系方式（邮箱、电话）

#### 2.2 评级

| 评级 | 标准 |
|------|------|
| **A** | 采购量大、持续，主供为竞争来源国 |
| **B** | 有量、匹配，需培育 |
| **C** | 小量/弱相关 → 仅入库 |
| **跳过** | 货代/无关 |

#### 2.3 挖决策人（enrich + LinkedIn 同时搜索，互补信息）⭐⭐⭐

**必须同时使用两个渠道**，不是二选一：

| 渠道 | 获取什么 | 互补关系 |
|------|---------|---------|
| **enrich MCP** | 公司通用邮箱（purchasing@, info@, sales@）、电话 | 公司级联系 |
| **LinkedIn 搜索** | 采购决策人姓名、职位、LinkedIn 个人页 | 个人级联系 |

**搜索渠道**：

1. **enrich MCP**（获取公司级联系邮箱）⭐
```bash
curl -X POST https://www.tradego.ai/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -H "mcp-session-id: {session_id}" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"enrich_company","arguments":{"domain":"{公司域名}"}}}'
```

2. **LinkedIn 搜索**（获取采购决策人个人信息）⭐
```bash
# 搜索采购决策人
search_people(keywords="Purchasing Manager", company="Acuity Brands", location="United States")

# 获取决策人资料
get_person_profile(url="linkedin.com/in/xxx")

# 发送连接请求
connect_with_person(url="linkedin.com/in/xxx", note="Hi, I'm from a LED lighting manufacturer in China...")
```

3. **Playwright 抓取 Google LinkedIn 搜索**（备用）
```
# 使用 Playwright 打开 Google 搜索
# URL: https://www.google.com/search?q=site:linkedin.com/in+"{公司名}"+purchasing+OR+procurement
```

> ⚠️ **两个渠道必须同时执行**：enrich 给公司邮箱，LinkedIn 给决策人个人页，缺一不可。

**采购决策人职位关键词**：

| 职位 | 英文 | 优先级 |
|------|------|--------|
| 采购经理 | Purchasing Manager | ⭐⭐⭐ |
| 采购总监 | Procurement Director | ⭐⭐⭐ |
| 供应链副总裁 | VP Supply Chain | ⭐⭐⭐ |
| 首席采购官 | Chief Procurement Officer | ⭐⭐⭐ |

**输出格式**（enrich + LinkedIn 合并展示）：

```
公司：Nucor Corporation
├── enrich 获取（公司级）
│   ├── purchasing@nucor.com（采购部门）
│   ├── sales@nucor.com（销售）
│   └── +1 800-xxx-xxxx（电话）
├── LinkedIn 获取（决策人级）⭐
│   ├── [Vernie N.] - VP, Sourcing
│   │   └── LinkedIn：linkedin.com/in/vernie-n-xxx
│   ├── [Lauren Nepsa] - Director of Sourcing
│   │   └── LinkedIn：linkedin.com/in/lauren-nepsa-xxx
│   └── [Derek Johnson] - Purchasing Manager
│       └── LinkedIn：linkedin.com/in/derekjohnson2
└── 社交
    ├── LinkedIn：linkedin.com/company/nucor
    └── Twitter：twitter.com/nucor
```
公司：Nucor Corporation
├── 通用联系
│   ├── purchasing@nucor.com
│   ├── sales@nucor.com
│   └── info@nucor.com
├── 采购决策人 ⭐
│   ├── [姓名] - Purchasing Manager
│   │   ├── 邮箱：xxx@nucor.com
│   │   └── LinkedIn：linkedin.com/in/xxx
│   └── [姓名] - Procurement Director
│       └── 邮箱：xxx@nucor.com
└── 社交
    ├── LinkedIn：linkedin.com/company/nucor
    └── Twitter：twitter.com/nucor
```

#### 2.4 沉淀到 CRM

- 先查重（按公司名/域名/邮箱）
- 创建客户（rating + ratingDesc 必填）
- 创建联系人

#### 2.5 双通道触达

**通道 A — 邮件**（有邮箱就发）

```
Subject: {公司名} - {产品} supply partner from China

Hi {姓名},

I noticed {公司名} imports {产品} from {来源国}.

We are a {产品} manufacturer from China with {优势}.

Would you be open to reviewing our catalog?

Best,
{签名}
```

**通道 B — WhatsApp**（有手机号就发，与邮件并行）

> ⚠️ 当前 WhatsApp MCP 未配置，将号码记录在报告中。

#### 本页小结 + 翻页

- 输出：处理 N 家；A/B/C 各几家；入库数；发信数
- `page += 1`，回到 Step A

---

## 结束：总报告

- 覆盖页数与采购商总数
- A/B 级客户名单（公司、国家、决策人）
- CRM 入库统计
- 触达统计
- 下一步建议

---

## 异常处理

- 工具失败：重试 1 次 → 跳过继续，不中断
- 无数据：换关键词或 HS 编码重试
- 严格逐页串行，不并行

## 额度监控

**enrich MCP 免费额度**：10 次/月

**监控方法**：
- 每次调用后记录剩余次数
- 接近限额（≤3 次）时提醒用户
- 额度用完时：等待下月刷新或注册新账号

**替代方案**（额度用完时）：
1. 用 Google 搜索 `site:linkedin.com/in "{公司名}" purchasing`
2. 用 Hunter.io 免费额度查找邮箱
3. 手动在公司官网查找联系方式

## 已安装 MCP

| MCP | 状态 | 用途 |
|-----|------|------|
| **playwright** | ✅ | 浏览器自动化，抓取 JS 页面 |
| **enrich** | ✅ | 域名→联系人邮箱电话 |
| **linkedin** | ⚠️ 待安装 | LinkedIn 搜索决策人、公司信息 |

## LinkedIn MCP（推荐安装）

**工具**：[stickerdaniel/linkedin-mcp-server](https://github.com/stickerdaniel/linkedin-mcp-server) ⭐ 2.5k

**安装**：
```bash
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

**核心工具**：
| 工具 | 说明 |
|------|------|
| `search_people` | 搜索 LinkedIn 用户（按职位/公司/地区） |
| `get_person_profile` | 获取个人资料（经历、教育、技能） |
| `get_company_profile` | 获取公司信息 |
| `get_company_employees` | 列出公司员工 |
| `connect_with_person` | 发送连接请求 |
| `send_message` | 发送消息 |

**使用场景**（在 skill 中）：
1. 搜索采购决策人：`search_people(keywords="Purchasing Manager", company="Acuity Brands")`
2. 获取决策人资料：`get_person_profile(url="linkedin.com/in/xxx")`
3. 发送连接请求：`connect_with_person(url="linkedin.com/in/xxx", note="...")`

## Claude Code 配置

```bash
# Playwright MCP（浏览器自动化）
claude mcp add playwright -- npx playwright-mcp

# enrich MCP（联系人查找）
claude mcp add enrich https://www.tradego.ai/mcp --transport http

# LinkedIn MCP（搜索决策人，推荐）
claude mcp add linkedin -- uvx mcp-server-linkedin@latest
```

## 避坑清单

1. ❌ **用 webfetch 抓 ImportYeti** → 必须用 Playwright
2. ❌ **并行拉取多页** → 必须逐页串行
3. ❌ **跳过 WhatsApp 只发邮件** → 双通道并行
4. ❌ **新邮箱直接猛发** → 先预热 2-4 周
5. ❌ **名单不验证就发** → 脏数据毁送达率
