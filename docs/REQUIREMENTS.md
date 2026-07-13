# tarrific.tv DSP Admin 需求文档

> 版本：v1.0（原型阶段）  
> 更新日期：2026-07-13  
> 说明：本文档基于当前 `index.html` 交互原型整理，按功能模块划分，供产品、设计、研发对齐使用。

---

## 1. 全局框架

### 1.1 产品定位

tarrific.tv DSP 管理后台，面向广告主/代理商，提供广告投放、素材管理、数据报表、应用管理、账户与计费等能力。

### 1.2 布局结构

| 区域 | 说明 |
|------|------|
| 左侧导航栏 | 固定宽度，深色主题，支持一级菜单 + 可展开子菜单 |
| 顶部栏 | 语言切换、用户头像与名称下拉 |
| 主内容区 | 面包屑、页面标题、业务内容 |

### 1.3 左侧导航菜单

| 一级菜单 | 子菜单 | 路由标识 |
|----------|--------|----------|
| Dashboard | Overview | `dashboard-overview` |
| Dashboard | Risk Control | `dashboard-risk` |
| Ads | — | `ads` |
| Manage Creative | — | `manage-creative` |
| Reports | Download Reports | `reports-download` |
| Reports | Ad Monitoring Report | `reports-ad-monitor` |
| Reports | Creative Monitoring Report | `reports-creative-monitor` |
| My Apps | — | `apps` |

> **说明：** Setting 已从左侧导航移除，入口迁移至右上角用户下拉。

### 1.4 顶部栏

| 功能 | 需求描述 |
|------|----------|
| 语言切换 | 展示当前语言（English），预留多语言扩展 |
| 用户下拉 | 点击用户名展开菜单 |
| Account settings | 进入账户设置页（独立布局，含 Back 返回） |
| Sign out | 退出登录 |

### 1.5 通用交互

- Toast 操作反馈
- 表格分页（Total、页码、每页条数）
- 卡片 + 表格统一视觉风格（主色 `#5bc0eb`）
- 模态框：标题、关闭、Cancel / Save 操作

---

## 2. Dashboard 模块

### 2.1 Overview（数据概览）

**入口：** 左侧 Dashboard → Overview

#### 2.1.1 筛选区

| 字段 | 类型 | 说明 |
|------|------|------|
| App | 下拉 | 选择应用，如 Test / Summer Promo US |
| Date Range | 日期文本 | 如 Jul 12, 2026 - Jul 12, 2026 |
| Timezone | 下拉 | UTC+0 / UTC+8 / UTC-5 |

#### 2.1.2 KPI 卡片（两排，7 列网格）

共 13 个指标，第一排 7 个、第二排 6 个：

| 序号 | 指标 | 说明 |
|------|------|------|
| 1 | Spend | 花费 |
| 2 | Revenue | 收入 |
| 3 | ROI | 投资回报率 |
| 4 | Installs | 安装量 |
| 5 | Impressions | 展示量 |
| 6 | Clicks | 点击量 |
| 7 | eCPI | 单次安装成本 |
| 8 | eCPM | 千次展示成本 |
| 9 | CTR | 点击率 |
| 10 | eCPC | 单次点击成本 |
| 11 | D0 | D0 指标 |
| 12 | D7 | D7 指标 |
| 13 | D30 | D30 指标 |

> Spend、Revenue、ROI 三列相邻展示。

#### 2.1.3 图表区

- 展示趋势图区域
- 无数据时显示空态（No data）

#### 2.1.4 数据表格

| 功能 | 说明 |
|------|------|
| Group by | 按 Date / Campaign / Ad Group 分组 |
| 视图切换 | Chart / Table 图标切换（保留） |
| 列顺序 | Date（或分组维度）+ Spend → Revenue → ROI → 其余指标 |
| 空态 | No data |

---

### 2.2 Risk Control（风控看板）

**入口：** 左侧 Dashboard → Risk Control

#### 2.2.1 账户健康概览

| 元素 | 说明 |
|------|------|
| 健康评分环 | 0–100 分，如 78 分 |
| 健康等级 | Good / 等级文案 |
| 摘要描述 | 账户整体状态说明 |
| 标签 | Account Active、Payment OK、Policy Compliant、Budget Pacing Warning 等 |

#### 2.2.2 KPI 卡片

- Available Balance（可用余额）
- Monthly Budget Used（月度预算使用率）
- Campaigns at Risk（风险 Campaign 数）
- Creative Pass Rate（素材通过率）

#### 2.2.3 Budget Progress（预算进度）

- Monthly Budget：月度花费 / 上限 / 百分比进度条
- Today's Spend：当日花费 / 上限 / 百分比进度条
- 7-Day Spend Pacing：7 日花费 pacing 柱状图

#### 2.2.4 Delivery Health（投放健康度）

| 指标 | 说明 | 状态 |
|------|------|------|
| Win Rate | 竞胜率 | Healthy |
| Creative Rejection | 素材拒审率 | Watch |
| Invalid Traffic (IVT) | 无效流量 | Low |
| Bid Floor Hit Rate | 底价命中率 | Normal |
| Under-delivery | 欠量 Campaign 数 | Action |

#### 2.2.5 Campaign Budget Status

表格列：Campaign、Daily Budget、Spent Today、Progress、Pacing

#### 2.2.6 Risk Alerts（风险告警）

按级别展示：

| 级别 | 示例 |
|------|------|
| 严重 (bad) | Daily budget exceeded |
| 警告 (warn) | Creative rejected、Under-delivery detected |
| 提示 (info) | Balance threshold notice |

每条含：标题、描述、时间戳。

#### 2.2.7 操作

- Refresh 按钮刷新看板数据

---

## 3. Ads 模块

**入口：** 左侧 Ads

### 3.1 页面结构

- 面包屑：Home > Ads
- 筛选：+ Filter、日期范围选择
- Tab：Campaign / Ad Group

### 3.2 Campaign / Ad Group 列表

| 列 | 说明 |
|----|------|
| 勾选 | 批量选择 |
| 开关 | 启用/暂停 |
| Name | Campaign / Ad Group 名称 |
| ID | 唯一标识 |
| Status | Active / Paused |
| Updated Time | 更新时间 |
| Created Time | 创建时间 |

### 3.3 工具栏

- New：新建
- Edit：编辑（需选中）
- Show Metrics、Custom Columns、Refresh

### 3.4 分页

Total、页码、每页条数（10 / page）

---

## 4. Manage Creative 模块

**入口：** 左侧 Manage Creative

### 4.1 Tab 结构

| Tab | 说明 |
|-----|------|
| Creative Group | 素材组管理 |
| Assets | 素材资产管理 |

---

### 4.2 Creative Group

#### 4.2.1 筛选

- Creative Group Name
- Combination Method（Programmatic / Manual）
- Ad Unit Name
- 操作：Upload Creative Group、Delete、Find

#### 4.2.2 列表字段

ID、Creative Group Name、Combination Method、Creative Type、Deliverable Ad Types、Country、Ad Unit、Created Time、Actions（Edit / Copy / Delete）

#### 4.2.3 新建/编辑弹窗

| 字段 | 说明 |
|------|------|
| Creative Group Name | 必填 |
| Creative Type | 多选：Banner / Native Image / Video 等 |
| Materials | Upload / Add Existing Creative / Delete Selected |
| Country | 默认 ALL |

Materials 表格：Creative ID、Asset Name、Type、Preview、Resolution、Source、Upload Time、Status

#### 4.2.4 从已有素材选择

- 搜索名称或 ID
- 多选确认添加

---

### 4.3 Assets

#### 4.3.1 工具栏

- New / Edit
- Show Metrics、Custom Columns、Refresh

#### 4.3.2 列表字段

Creative Name、Creative ID、Creative Type、Campaign Name/ID、Ad Group Name/ID、Status、Updated/Created Time、Schedule

#### 4.3.3 新建/编辑素材页

| 区域 | 内容 |
|------|------|
| 类型切换 | Banner / Native Image / Video / Video EndCard / Playable |
| 上传区 | 拖拽或点击上传图片/视频 |
| 表单 | Title、Description、Native CTA Text |
| 手机预览 | 实时预览广告卡片效果 |

---

## 5. Reports 模块

### 5.1 Download Reports

**入口：** Reports → Download Reports

| 功能 | 说明 |
|------|------|
| 搜索 | 按 Report name / Create by 搜索 |
| + Create | 创建报表（预留） |
| 列表列 | Report name、Time range、Rows/size、Status、Create date、Create by、Actions |
| Actions | Download、View、Delete |

---

### 5.2 Ad Monitoring Report（广告报表）

**入口：** Reports → Ad Monitoring Report

#### 5.2.1 筛选条件

| 字段 | 类型 |
|------|------|
| Timezone | 下拉 |
| Date Range | 文本 |
| Campaign Name / ID | 文本 |
| Ad Group Name / ID | 文本 |
| Creative Name / ID | 文本 |
| Creative Group Name / ID | 文本 |
| Creative Type | 下拉 |
| Delivery Region | 文本 |

#### 5.2.2 操作按钮

- Search Data
- Export Data

> **已移除：** Advanced Options、CSV Batch Block Traffic

#### 5.2.3 Data Dimensions（数据维度）

可多选：Day、Hour、Week、Month、Campaign Name/ID、Ad Group Name/ID、Creative Group Name/ID、Creative Name/ID、Creative Type、Country/Region

#### 5.2.4 Data Indicators（数据指标）

- Configure Columns 打开列配置弹窗
- 默认选中 12 列

#### 5.2.5 列配置弹窗（Columns）

**左侧 Selected columns：** 已选列，支持 Remove / Remove group / Remove all、拖拽排序

**右侧 Unselected columns：** 未选列，支持 Add / Add group / Add all

**搜索：** Search columns by name

**字段分组：**

| 分组 | 字段 |
|------|------|
| Basic | Impressions, Clicks, CTR, Installs, CPI, IR, Spend, **Revenue**, **ROI**, CPM |
| Campaign goals | CPP goal, CPE goal, ROAS goal |
| Retention | D1/D3/D7/D14/D28/D30 retention |
| IAA revenue | D0–D30 IAA rev、1Y IAA rev；D0–D30 IAA ROAS |
| IAP revenue | D0–D30 IAP rev、1Y IAP rev；D0–D30 IAP ROAS |
| Total revenue | D0–D30 total rev、1Y total rev；D0–D30/D60/D90/D180 total ROAS |
| Purchases | D0–D30 unique purchasers、Unique purchasers、D0–D30 CPP |
| Target events | D0–D30 unique target events、1Y unique target events、D0–D30 target event CPE |

> 所有 D0 类型日维度字段延伸至 **D30**（序列：D0, D1, D3, D7, D14, D28, D30）。

**默认选中列：**

Impressions, Clicks, CTR, Installs, CPI, IR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS

#### 5.2.6 数据表格

- 维度列 + 指标列动态组合
- 列头支持排序
- 空态：No data
- 分页：50 / page

---

### 5.3 Creative Monitoring Report（素材报表）

**入口：** Reports → Creative Monitoring Report

结构与 **Ad Monitoring Report** 一致，区别：

- 默认 Data Dimensions 选中：Day、Ad Group Name、Creative Name、Creative ID
- 共用同一套 Columns 字段配置
- 同样已移除 Advanced Options、CSV Batch Block Traffic

---

## 6. My Apps 模块

**入口：** 左侧 My Apps

### 6.1 页面结构

- 标题 + **+ New App** 按钮（绿色）
- 筛选栏 + 提示文案

### 6.2 筛选条件

| 字段 | 类型 | 选项 |
|------|------|------|
| App Name | 文本 | 模糊搜索 |
| Platform | 下拉 | iOS / Android |
| Status | 下拉 | Active / Paused |

**提示：** Spend, CVR and CPI values are calculated for the last 7 days.

### 6.3 列表字段

| 列 | 说明 |
|----|------|
| 勾选 + 开关 | 批量选择 / 启用暂停 |
| App Name | 图标 + 名称 |
| Status | Active / Paused 徽章 |
| Actions | Edit、更多菜单（▾） |
| App ID | Bundle ID，如 com.test |
| OS | iOS / Android |
| Timezone | UTC+0 / UTC+8 等 |
| Active Campaigns | 活跃 Campaign 数 |
| Spend | 近 7 天花费 |
| CVR | 近 7 天转化率 |
| CPI | 近 7 天单次安装成本 |
| Linked Ad Units | 关联 Ad Unit 数 |

### 6.4 新增 / 编辑 App 弹窗

| 字段 | 必填 | 说明 |
|------|------|------|
| App Name | 是 | 应用名称 |
| Platform | 是 | iOS / Android |
| App ID (Bundle ID) | 是 | 如 com.example.app |
| Timezone | 否 | UTC+0 / UTC+8 / UTC-5 |

### 6.5 交互

- 开关切换 Active / Paused，Toast 反馈
- 新建 App 默认 Active，指标初始为 `--`
- 分页：10 / page

---

## 7. Account Settings 模块

**入口：** 右上角用户下拉 → Account settings

### 7.1 布局

- 独立全屏布局（非标准侧栏内容区）
- 左侧 Tab 导航 + 右侧内容
- Back 返回进入设置前的页面

### 7.2 Account Info

| 字段 | 说明 |
|------|------|
| 账户头像 | 首字母缩写 |
| Account ID | 只读展示 |
| Account name | 可编辑 |
| Company name | 可编辑 |
| Tax ID | 可选 |
| Website to promote | https:// + 域名 |
| Save | 保存 |

### 7.3 Members（子账号管理）

#### 7.3.1 Account Owner 卡片

展示 Owner 头像、邮箱

#### 7.3.2 All Users 列表

| 列 | 说明 |
|----|------|
| User email | 用户邮箱 |
| Last signed-in (UTC) | 最后登录 |
| Access level | Owner / Admin / Read-only |
| Expiration (UTC) | Never / 30 days / 90 days |
| App access | All apps / Selected apps |

#### 7.3.3 筛选与操作

- Search users by email
- Access level 筛选
- **Invite new user** 按钮

#### 7.3.4 Invite New User 弹窗

| 字段 | 说明 |
|------|------|
| Email addresses | 必填，逗号分隔多个 |
| Access level | Read-only / Admin |
| Access expiration | Never / 30 days / 90 days |
| App access | All apps / Selected apps |

---

### 7.4 Payment & Billing

#### 7.4.1 信息横幅

计费说明 + Schedule a call / Learn more 链接

#### 7.4.2 Balance 卡片

- 当前余额
- Add funds 按钮
- 明细：Added funds、Ad credit、Held for spend

#### 7.4.3 Payment Method 卡片

- 无卡：空态 + Add payment method
- 有卡：展示 VISA 末四位、过期时间、Auto reload 标签

#### 7.4.4 All Billing Activities

| 功能 | 说明 |
|------|------|
| Receive receipt emails | 开关 |
| Search by ID | 搜索 |
| Filter / Export data | 筛选导出 |
| 时间范围 | All time |
| Category / Status | 筛选 Chip |
| 表格列 | ID、Time、Category、Description、Amount、Payment method、Status |

#### 7.4.5 Billing Address

- 展示当前地址或占位
- Add / Edit billing information 按钮

#### 7.4.6 Add Card 弹窗

- Card details：卡号、Expiry、CVV
- Billing address：Full name、Country、Postal code

#### 7.4.7 Add Billing Information 弹窗

- 提示：提交后不可修改
- Company billing address 表单
- Powered by Stripe

---

## 8. 非功能需求（原型阶段）

| 项 | 说明 |
|----|------|
| 技术栈 | Vue 3 CDN 单页 HTML 原型 |
| 数据 | 当前为 Mock 数据，无后端 API |
| 浏览器 | 现代浏览器 Chrome / Safari / Edge |
| 响应式 | 桌面端优先，最小宽度建议 1280px |
| 国际化 | UI 以英文为主，部分原型保留中文参考 |

---

## 9. 模块依赖关系

```text
My Apps
  └── Dashboard Overview（App 筛选）
  └── Reports（按 App/Campaign 维度查询）

Ads
  └── Manage Creative（Campaign / Ad Group 关联）
  └── Reports（Campaign / Ad Group 维度）

Manage Creative
  └── Reports（Creative 维度）

Account Settings
  └── Members（子账号权限）
  └── Payment & Billing（账户充值与投放预算）
  └── Dashboard Risk Control（余额、预算告警）
```

---

## 10. 待后续迭代（Out of Scope · 原型未实现）

- 真实 API 对接与鉴权
- URL 路由（Vue Router）
- 多语言完整 i18n
- Dashboard Overview 图表真实数据
- Ads / Creative 完整 CRUD 与后端联动
- Members 权限细粒度（App 级勾选）
- Stripe 真实支付集成
- 报表异步生成与下载
- 移动端适配

---

## 附录：页面路由一览

| 模块 | 页面 | page 标识 |
|------|------|-----------|
| Dashboard | Overview | `dashboard-overview` |
| Dashboard | Risk Control | `dashboard-risk` |
| Ads | Ads | `ads` |
| Creative | Manage Creative | `manage-creative` |
| Reports | Download Reports | `reports-download` |
| Reports | Ad Monitoring | `reports-ad-monitor` |
| Reports | Creative Monitoring | `reports-creative-monitor` |
| Apps | My Apps | `apps` |
| Settings | Account Settings | `account-settings` |
