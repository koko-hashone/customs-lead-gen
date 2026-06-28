# ImportYeti 使用指南（Playwright 版）

## 简介

ImportYeti 是免费的美国海关提单数据查询工具。**页面是 JS 渲染的，必须用 Playwright MCP 抓取。**

**覆盖范围**：仅美国海运进口数据

## Playwright 抓取步骤

### 1. 搜索采购商

```bash
# Step 1: 打开搜索页
playwright_navigate(url="https://www.importyeti.com/search?q=LED+lighting&type=company")

# Step 2: 等待 JS 渲染（至少 5 秒）
playwright_wait(time=5)

# Step 3: 提取页面内容
playwright_get_content()
```

**解析要点**：
- 公司名称
- 总提单数（Total Sea Shipments）
- 供应商国家分布
- 产品描述/HS 编码

### 2. 查看公司详情

```bash
# Step 1: 打开公司详情页
playwright_navigate(url="https://www.importyeti.com/company/{公司名}")

# Step 2: 等待加载
playwright_wait(time=5)

# Step 3: 提取完整信息
playwright_get_content()
```

**可提取信息**：
- 公司地址、电话、官网
- 总提单数、月均 TEU
- 供应商列表（前 10）
- 进口国家分布
- HS 编码分类
- 最近提单明细

### 3. 分页抓取

```bash
# 第 2 页
playwright_navigate(url="https://www.importyeti.com/search?q=LED+lighting&type=company&page=2")
playwright_wait(time=5)
playwright_get_content()
```

**终止条件**：当某页返回空列表或内容与上一页相同。

## 搜索关键词技巧

| 产品 | 搜索关键词 |
|------|-----------|
| LED 灯具 | `LED lighting`, `LED fixture`, `LED lamp` |
| 光伏支架 | `solar mounting`, `solar racking`, `solar bracket` |
| 不锈钢管 | `stainless steel pipe`, `stainless tube` |
| 锂电池 | `lithium battery`, `li-ion battery` |

## 公司详情页关键字段

| 字段 | 含义 | 用途 |
|------|------|------|
| Total Sea Shipments | 历史海运提单总数 | 判断采购规模 |
| Avg. TEU per Month | 月均集装箱数 | 判断采购频率 |
| Suppliers | 前 10 供应商 | 了解供应商来源 |
| Imports Per Country | 进口国家分布 | 了解竞争格局 |
| HTS Codes | HS 编码分类 | 确认产品类型 |
| Recent Sea Shipments | 最近提单 | 判断活跃度 |

## 常见问题

### Q: 为什么 webfetch 拿不到数据？
A: ImportYeti 是 JS 渲染的，webfetch 只能拿到空的 HTML 模板。必须用 Playwright MCP 执行 JavaScript 后再提取内容。

### Q: 如何判断页面加载完成？
A: 使用 `playwright_wait(time=5)` 等待 5 秒，或使用 `playwright_wait_for_selector` 等待特定元素出现。

### Q: 公司名 URL 格式是什么？
A: 全小写，用连字符连接。如 `acuity-brands-lighting`、`satco-products`。
