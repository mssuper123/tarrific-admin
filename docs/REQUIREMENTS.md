# tarrific.tv DSP Admin 产品需求文档

> **版本：** v1.1  
> **更新日期：** 2026-07-13  
> **文档状态：** 基于交互原型（`index.html`）整理  
> **适用对象：** 产品、设计、前端、后端、测试

---

## 目录

1. [全局框架](#1-全局框架)
2. [Dashboard · Overview](#2-dashboard--overview)
3. [Dashboard · Risk Control](#3-dashboard--risk-control)
4. [Ads](#4-ads)
5. [Manage Creative · Creative Group](#5-manage-creative--creative-group)
6. [Manage Creative · Creatives](#6-manage-creative--creatives)
7. [Manage Creative · 新建/编辑素材页](#7-manage-creative--新建编辑素材页)
8. [Reports · Download Reports](#8-reports--download-reports)
9. [Reports · Ad Monitoring Report](#9-reports--ad-monitoring-report)
10. [Reports · Creative Monitoring Report](#10-reports--creative-monitoring-report)
11. [My Apps](#11-my-apps)
12. [Account Settings · Account Info](#12-account-settings--account-info)
13. [Account Settings · Members](#13-account-settings--members)
14. [Account Settings · Payment & Billing](#14-account-settings--payment--billing)
15. [附录](#15-附录)

---

## 1. 全局框架

### 1.1 模块说明

tarrific.tv DSP 管理后台，面向广告主及代理商运营人员，提供广告投放管理、素材管理、数据报表、应用管理、账户与计费等功能。当前为单页 Vue 原型，页面通过 `page` 变量切换，无 URL 路由。

**整体布局：**

| 区域 | 宽度/位置 | 说明 |
|------|-----------|------|
| 左侧导航栏 | 200px 固定 | 深色主题，一级菜单 + 可展开子菜单 |
| 顶部栏 | 全宽 | 语言、用户下拉 |
| 主内容区 | 剩余空间 | 面包屑 + 标题 + 业务内容 |

### 1.2 导航菜单

| 一级菜单 | 子菜单 | page 标识 | 默认页 |
|----------|--------|-----------|--------|
| Dashboard | Overview | `dashboard-overview` | ✓（Overview 为 Dashboard 默认子页） |
| Dashboard | Risk Control | `dashboard-risk` | |
| Ads | — | `ads` | |
| Manage Creative | — | `manage-creative` | |
| Reports | Download Reports | `reports-download` | |
| Reports | Ad Monitoring Report | `reports-ad-monitor` | |
| Reports | Creative Monitoring Report | `reports-creative-monitor` | |
| My Apps | — | `apps` | |

> Account Settings 不在左侧导航，入口位于右上角用户下拉 → **Account settings**。

### 1.3 顶部栏操作

| 元素 | 类型 | 说明 |
|------|------|------|
| 语言切换 | 静态展示 | 当前显示 English，预留 i18n |
| 用户头像 + 名称 | 下拉触发 | 点击展开菜单 |
| Account settings | 菜单项 | 进入账户设置（独立布局） |
| Sign out | 菜单项 | 退出登录 |

### 1.4 通用规范

| 规范项 | 说明 |
|--------|------|
| 操作反馈 | Toast 提示成功/失败 |
| 分页 | Total 条数、上一页/下一页、页码、每页条数 |
| 空态 | 列表无数据时居中展示文案/图标 |
| 模态框 | 标题 + 关闭(X) + Cancel + 主操作按钮 |
| 主色调 | `#5bc0eb` |

---

## 2. Dashboard · Overview

### 2.1 模块说明

**入口：** 左侧 Dashboard → Overview  
**面包屑：** Home > Dashboard  
**目标用户：** 运营/投放人员  
**核心目标：** 按 App + 时间范围快速查看账户级核心投放指标（Spend / Revenue / ROI 优先），并通过图表与明细表下钻分析。

本页为 **只读看板**，不含增删改操作；数据来源于报表聚合（原型阶段为 Mock）。

---

### 2.2 筛选

| # | 字段名 | 控件类型 | 选项/占位 | 是否必填 | 说明 |
|---|--------|----------|-----------|--------|------|
| 1 | App | 下拉选择 | Test / Summer Promo US / Brand Awareness EU | 否 | 按应用过滤全局 KPI 与表格 |
| 2 | Date Range | 文本输入 | 如 `Jul 12, 2026 - Jul 12, 2026` | 否 | 统计时间范围，原型为文本框 |
| 3 | Timezone | 下拉选择 | UTC+0 / UTC+8 / UTC-5 | 否 | 时区 |

> 筛选变更后应刷新 KPI 卡片、图表、表格（原型未接 API，仅 UI）。

---

### 2.3 列表 / 展示内容

#### 2.3.1 KPI 卡片区（非表格，13 个指标，7 列 × 2 排）

| # | 字段 | 英文名 | 格式示例 | 备注 |
|---|------|--------|----------|------|
| 1 | 花费 | Spend | $0 | 与 Revenue、ROI 相邻，第一排前三 |
| 2 | 收入 | Revenue | $0 | |
| 3 | 投资回报率 | ROI | 0% | |
| 4 | 安装量 | Installs | 0 | |
| 5 | 展示量 | Impressions | 0 | |
| 6 | 点击量 | Clicks | 0 | |
| 7 | 单次安装成本 | eCPI | $0 | 第一排第 7 个 |
| 8 | 千次展示成本 | eCPM | $0 | 第二排 |
| 9 | 点击率 | CTR | 0% | |
| 10 | 单次点击成本 | eCPC | $0 | |
| 11 | D0 指标 | D0 | 0% | 留存/ROAS 类汇总 |
| 12 | D7 指标 | D7 | 0% | |
| 13 | D30 指标 | D30 | 0% | |

#### 2.3.2 图表区

| 元素 | 说明 |
|------|------|
| 趋势图占位 | 展示 Spend 等核心指标随时间变化 |
| 空态 | 无数据时显示飞机图标 + "No data" |

#### 2.3.3 明细表格

**Group by 分组维度（下拉，三选一）：**

| 值 | 作为首列展示 |
|----|--------------|
| Date | 日期 |
| Campaign | Campaign 名称 |
| Ad Group | Ad Group 名称 |

**数据列（固定顺序，Group 维度列之后）：**

| # | 列名 |
|---|------|
| 1 | Spend |
| 2 | Revenue |
| 3 | ROI |
| 4 | Installs |
| 5 | Impressions |
| 6 | Clicks |
| 7 | eCPI |
| 8 | eCPM |
| 9 | CTR |
| 10 | eCPC |
| 11 | D0 |
| 12 | D7 |
| 13 | D30 |

**空态：** No data

---

### 2.4 操作

| 位置 | 操作 | 触发方式 | 说明 |
|------|------|----------|------|
| 表格工具栏 | 切换 Chart 视图 | 点击图表图标 | 切换至图表模式（原型保留图标） |
| 表格工具栏 | 切换 Table 视图 | 点击表格图标 | 切换至表格模式 |
| — | 无增删改 | — | 本页只读 |

---

## 3. Dashboard · Risk Control

### 3.1 模块说明

**入口：** 左侧 Dashboard → Risk Control  
**面包屑：** Home > Dashboard > Risk Control  
**目标用户：** 账户管理员、投放负责人  
**核心目标：** 监控账户健康度、预算消耗进度、投放质量指标，并对异常风险进行告警，辅助及时干预。

本页为 **只读监控看板**，以卡片 + 表格 + 告警列表形式呈现，不含业务对象的新增/编辑。

---

### 3.2 筛选

| # | 字段 | 说明 |
|---|------|------|
| — | 无独立筛选栏 | 展示当前账户全局风控数据 |

---

### 3.3 列表 / 展示内容

#### 3.3.1 账户健康（Hero 区）

| 元素 | 说明 |
|------|------|
| Health Score | 0–100 环形评分 |
| Health Label | 如 Good |
| Summary | 账户状态文字摘要 |
| Tags | Account Active / Payment OK / Policy Compliant / Budget Pacing Warning |

#### 3.3.2 KPI 卡片（4 个）

| 字段 | 说明 |
|------|------|
| Available Balance | 可用余额 + 副文案 |
| Monthly Budget Used | 月度预算使用率 |
| Campaigns at Risk | 存在风险的 Campaign 数量 |
| Creative Pass Rate | 素材通过率及环比 |

#### 3.3.3 Budget Progress 卡片

| 元素 | 说明 |
|------|------|
| Monthly Budget | 已花费 / 上限 / 百分比进度条 |
| Today's Spend | 当日花费 / 上限 / 百分比进度条 |
| 7-Day Spend Pacing | 7 天柱状图（Mon–Sun） |

#### 3.3.4 Delivery Health 列表

| 指标 | 副说明 | 状态标签 |
|------|--------|----------|
| Win Rate | Bid win / total bids | Healthy |
| Creative Rejection | Rejected / submitted | Watch |
| Invalid Traffic (IVT) | Fraud & invalid impressions | Low |
| Bid Floor Hit Rate | Bids below floor | Normal |
| Under-delivery | Below 80% daily goal | Action |

#### 3.3.5 Campaign Budget Status 表格

| # | 列名 | 说明 |
|---|------|------|
| 1 | Campaign | Campaign 名称 |
| 2 | Daily Budget | 日预算 |
| 3 | Spent Today | 当日已花费 |
| 4 | Progress | 进度条 + 百分比 |
| 5 | Pacing | On Pace / Over Budget / Near Cap / Under Pace |

#### 3.3.6 Risk Alerts 列表

| # | 列/元素 | 说明 |
|---|---------|------|
| 1 | 级别 | bad（红）/ warn（黄）/ info（蓝） |
| 2 | Title | 告警标题 |
| 3 | Description | 详细描述 |
| 4 | Time | 相对时间，如 2h ago |

---

### 3.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 页头右侧 | ↻ Refresh | 手动刷新看板数据，Toast 提示 |

---

## 4. Ads

### 4.1 模块说明

**入口：** 左侧 Ads  
**面包屑：** Home > Ads  
**目标用户：** 投放运营  
**核心目标：** 管理 Campaign 与 Ad Group 的启用状态，查看基础信息及时间戳，支持新建与编辑（原型部分能力为占位）。

---

### 4.2 筛选

| # | 字段 | 控件类型 | 说明 |
|---|------|----------|------|
| 1 | + Filter | 按钮 | 打开高级筛选（原型占位） |
| 2 | Date Range | 日期范围选择器 | 如 `2026-07-03 ~ 2026-07-09`，影响指标展示（若开启 Show Metrics） |

**Tab 切换（影响列表名称列）：**

| Tab | 列表对象 | Name 列标题 |
|-----|----------|-------------|
| Campaign | 广告计划 | Campaign Name |
| Ad Group | 广告组 | Ad Group Name |

---

### 4.3 列表

| # | 列名 | 说明 |
|---|------|------|
| 1 | 勾选框 | 批量选择 |
| 2 | 开关 | 启用/暂停，绿色为 Active |
| 3 | Name | Campaign Name 或 Ad Group Name |
| 4 | ID | 唯一标识 |
| 5 | Status | Active（绿点）/ Paused（灰点） |
| 6 | Updated Time | 最后更新时间 |
| 7 | Created Time | 创建时间 |

**分页：** 10 / page

---

### 4.4 操作

| 位置 | 操作 | 条件/说明 |
|------|------|-----------|
| 工具栏 | New | 新建 Campaign / Ad Group（原型占位） |
| 工具栏 | Edit ▾ | 需先勾选一条，否则 disabled |
| 工具栏 | Show Metrics | 勾选后展示指标列（原型占位） |
| 工具栏 | Custom Columns | 自定义列（原型占位） |
| 工具栏 | ↻ Refresh | 刷新列表 |
| 行级 | 开关切换 | 切换 Active / Paused |

---

## 5. Manage Creative · Creative Group

### 5.1 模块说明

**入口：** 左侧 Manage Creative → Tab **Creative Group**  
**面包屑：** Home > Manage Creative  
**目标用户：** 素材运营  
**核心目标：** 管理素材组（Creative Group），将多种素材类型组合并关联 Campaign / Ad Group，支持新建、编辑、复制、删除。

---

### 5.2 筛选

| # | 字段 | 控件类型 | 选项/占位 | 说明 |
|---|------|----------|-----------|------|
| 1 | Creative Group Name | 文本输入 | `- Creative Group Name -` | 模糊匹配名称 |
| 2 | Combination Method | 下拉 | 空 / Programmatic Creative / Manual Combination | 组合方式 |
| 3 | Ad Unit Name | 文本输入 | `- Ad Unit Name -` | 模糊匹配 Ad Unit |

> 输入筛选条件后列表实时过滤（`filteredGroups`）。

---

### 5.3 列表

| # | 列名 | 说明 |
|---|------|------|
| 1 | 勾选框 | 批量选择 |
| 2 | ID | 素材组 ID |
| 3 | Creative Group Name | 名称 |
| 4 | Campaign Name | 关联 Campaign |
| 5 | Campaign ID | |
| 6 | Ad Group Name | 关联 Ad Group |
| 7 | Ad Group ID | |
| 8 | Combination Method | Programmatic Creative / Manual Combination |
| 9 | Creative Type | 标签形式，可多类型 |
| 10 | Deliverable Ad Types | 可投放广告类型 |
| 11 | Country | 国家，默认 ALL |
| 12 | Ad Unit | Ad Unit 名称/ID |
| 13 | Created Time | 创建时间 |
| 14 | Actions | 行内操作 |

**分页：** 20 / page  
**空态：** No creative groups found

---

### 5.4 操作

#### 5.4.1 页级操作

| 操作 | 说明 |
|------|------|
| New Creative Group | 打开新建弹窗 |

#### 5.4.2 行级操作

| 操作 | 说明 |
|------|------|
| Edit | 打开编辑弹窗，回填数据 |
| Copy | 复制一条素材组，名称追加 `(Copy)` |
| Delete | 删除单条，Toast 确认 |

#### 5.4.3 新建/编辑弹窗（New / Edit Creative Group）

**弹窗字段：**

| # | 字段 | 必填 | 控件 | 说明 |
|---|------|------|------|------|
| 1 | Creative Group Name | 是 | 文本 | 如 S1S2nativevideo |
| 2 | Creative Type | 否 | 多选按钮组 | Banner / Native Image / Video / Video EndCard / Playable |
| 3 | Materials | 是（至少 1 条） | 表格 + 工具栏 | 见下表 |
| 4 | Country | 否 | 文本 | 默认 ALL |

**Materials 工具栏操作：**

| 操作 | 说明 |
|------|------|
| Upload | 上传图片/视频/html/zip |
| Add Existing Creative | 从已有 Creatives 多选添加 |
| Delete Selected | 删除选中的 Material 行 |

**Materials 列表列：**

| # | 列名 |
|---|------|
| 1 | 勾选框 |
| 2 | Creative ID |
| 3 | Asset Name |
| 4 | Type |
| 5 | Preview |
| 6 | Resolution |
| 7 | Source（Upload / Existing） |
| 8 | Upload Time |
| 9 | Status |

**弹窗底部：** Cancel / Save Creative Group

**Add Existing Creative 子弹窗：**

| 元素 | 说明 |
|------|------|
| 搜索框 | 按名称或 ID 搜索 |
| 列表 | 可多选 Creative |
| Cancel / Add Selected | 确认添加 |

---

## 6. Manage Creative · Creatives

### 6.1 模块说明

**入口：** 左侧 Manage Creative → Tab **Creatives**  
**目标用户：** 素材运营  
**核心目标：** 管理单条广告素材（Creative/Asset），查看关联 Campaign/Ad Group，控制启用状态，支持新建与编辑。

---

### 6.2 筛选

| # | 说明 |
|---|------|
| — | 当前无独立筛选栏，全量展示 `assets` 列表 |

---

### 6.3 列表

| # | 列名 | 说明 |
|---|------|------|
| 1 | 勾选框 | 批量选择 |
| 2 | 开关 | 启用/暂停素材 |
| 3 | Creative Name | 素材名称 |
| 4 | Creative ID | |
| 5 | Creative Type | Banner / Video 等标签 |
| 6 | Campaign Name | |
| 7 | Campaign ID | |
| 8 | Ad Group Name | |
| 9 | Ad Group ID | |
| 10 | Status | Active / Paused |
| 11 | Updated Time | |
| 12 | Created Time | |
| 13 | Schedule | 投放排期，如 Always |

**分页：** 10 / page

---

### 6.4 操作

| 位置 | 操作 | 条件/说明 |
|------|------|-----------|
| 工具栏 | New | 进入新建素材页（`mcView=asset-form`） |
| 工具栏 | Edit ▾ | 需勾选恰好 1 条 |
| 工具栏 | Show Metrics | 显示指标列（原型占位） |
| 工具栏 | Custom Columns | 自定义列（原型占位） |
| 工具栏 | ↻ Refresh | 刷新，Toast 提示 |
| 行级 | 开关 | 切换 Active/Paused，Toast 提示 |

---

## 7. Manage Creative · 新建/编辑素材页

### 7.1 模块说明

**入口：** Creatives Tab → New / Edit  
**面包屑：** Home > Manage Creative > New Asset / Edit Asset  
**核心目标：** 上传素材文件、填写文案、选择类型，并实时预览移动端广告效果；可选关联 Creative Group。

---

### 7.2 筛选

无。

---

### 7.3 表单字段

| # | 字段 | 控件 | 说明 |
|---|------|------|------|
| 1 | Creative Type | 分段按钮 | Banner / Native Image / Video / Video EndCard / Playable |
| 2 | 上传区 | 拖拽/点击 | 支持图片或视频，单次 1 个 |
| 3 | Title | 文本 | 广告标题 |
| 4 | Description | 多行文本 | 广告描述 |
| 5 | Native CTA Text | 文本 | 按钮文案，如 Install |

**右侧：** 手机 Mock 预览（For You 信息流广告卡片）

**Apply to Creative Group（可选区块）：**

| 筛选 | Creative Group Name / Ad Unit Name + Find 按钮 |
| 列表 | 勾选关联的 Creative Group（ID、Name、Campaign、Ad Unit、Created） |

---

### 7.4 操作

| 操作 | 说明 |
|------|------|
| Back to List | 返回 Creatives 列表 |
| Save | 保存素材，新建或更新，返回列表 |

---

## 8. Reports · Download Reports

### 8.1 模块说明

**入口：** Reports → Download Reports  
**面包屑：** Home > Reports > Download Reports  
**核心目标：** 查看已生成的离线报表任务，支持搜索、下载、查看、删除。

---

### 8.2 筛选

| # | 字段 | 控件 | 匹配规则 |
|---|------|------|----------|
| 1 | Search | 文本 | 匹配 Report name 或 Create by（不区分大小写） |

---

### 8.3 列表

| # | 列名 | 说明 |
|---|------|------|
| 1 | Report name | 报表名称 |
| 2 | Time range | 统计时间范围 |
| 3 | Rows/size | 行数/文件大小 |
| 4 | Status | 如 Available for download until yyyy-mm-dd |
| 5 | Create date | 创建日期 |
| 6 | Create by | 创建人 |
| 7 | Actions | 图标操作列 |

**分页：** 10 / page  
**空态：** No reports found

---

### 8.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 页头 | + Create | 创建报表任务（原型 Toast 占位） |
| 行级 | Download | 下载报表文件 |
| 行级 | View | 预览报表 |
| 行级 | Delete | 删除记录 |

---

## 9. Reports · Ad Monitoring Report

### 9.1 模块说明

**入口：** Reports → Ad Monitoring Report  
**面包屑：** Home > Reports > Ad Monitoring Report  
**核心目标：** 按多维度筛选广告数据，自定义维度和指标列，查询并导出广告层级投放效果报表。

---

### 9.2 筛选

#### 9.2.1 查询条件（3 行 × 4 列网格）

| # | 字段 | 控件 | 占位/选项 |
|---|------|------|-----------|
| 1 | Timezone | 下拉 | UTC+8 / UTC+0 / UTC-5 等 |
| 2 | Date Range | 文本 | 如 2026-07-08 - 2026-07-10 |
| 3 | Campaign Name | 文本 | Campaign Name |
| 4 | Campaign ID | 文本 | Campaign ID |
| 5 | Ad Group Name | 文本 | Ad Group Name |
| 6 | Ad Group ID | 文本 | Ad Group ID |
| 7 | Creative Name | 文本 | Creative Name |
| 8 | Creative ID | 文本 | Creative ID |
| 9 | Creative Group Name | 文本 | Creative Group Name |
| 10 | Creative Group ID | 文本 | Creative Group ID |
| 11 | Creative Type | 下拉 | 空 / Banner / Native Image / Video / Video EndCard / Playable |
| 12 | Delivery Region | 文本 | Delivery Region |

#### 9.2.2 Data Dimensions（多选 checkbox，决定表格维度列）

| 维度 | 默认选中 |
|------|----------|
| Day | ✓ |
| Hour | |
| Week | |
| Month | |
| Campaign Name | ✓ |
| Campaign ID | |
| Ad Group Name | ✓ |
| Ad Group ID | |
| Creative Group Name | |
| Creative Group ID | |
| Creative Name | |
| Creative ID | |
| Creative Type | |
| Country / Region | |

#### 9.2.3 Data Indicators（指标列，通过 Configure Columns 配置）

默认选中 12 列：`Impressions, Clicks, CTR, Installs, CPI, IR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS`

> 完整可选字段见 [附录 A：报表指标字段库](#附录-a报表指标字段库)。

---

### 9.3 列表（查询结果表）

**列组成：** 已选 Data Dimensions（按勾选顺序） + 已选 Data Indicators（按 Configure Columns 顺序）

**列头：** 支持点击排序（↑ / ↓）

**分页：** 50 / page

**空态：** 未点击 Search Data 前为空；搜索无结果时显示 No data + 飞机图标

**示例数据列（Mock）：** Day, Campaign Name, Ad Group Name, Impressions, Clicks, CTR, Installs, CPI, IR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS

---

### 9.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 筛选区 | Search Data | 执行查询，Toast 返回条数 |
| 筛选区 | Export Data | 导出当前结果 |
| 指标区 | Configure Columns | 打开列配置弹窗 |
| 列配置弹窗 | 搜索列名 | Search columns by name |
| 列配置弹窗 | Add / Remove / Add group / Remove group | 管理已选/未选列 |
| 列配置弹窗 | Remove all / Add all | 批量操作 |
| 列配置弹窗 | Apply | 应用并关闭 |
| 列配置弹窗 | Cancel | 取消 |

> **已明确移除：** Advanced Options、CSV Batch Block Traffic

---

## 10. Reports · Creative Monitoring Report

### 10.1 模块说明

**入口：** Reports → Creative Monitoring Report  
**面包屑：** Home > Reports > Creative Monitoring Report  
**核心目标：** 与广告报表结构一致，默认以素材维度（Creative Name / Creative ID）为主进行分析。

---

### 10.2 筛选

与 [§9.2 广告报表](#92-筛选) **完全相同**（12 个查询字段 + Data Dimensions + Data Indicators）。

**Data Dimensions 默认选中差异：**

| 维度 | 默认选中 |
|------|----------|
| Day | ✓ |
| Ad Group Name | ✓ |
| Creative Name | ✓ |
| Creative ID | ✓ |
| 其余 | 未选中 |

---

### 10.3 列表

与 [§9.3](#93-列表查询结果表) 相同规则；Mock 数据以素材粒度为主。

---

### 10.4 操作

与 [§9.4](#94-操作) 相同；Configure Columns 共用同一套字段库。

---

## 11. My Apps

### 11.1 模块说明

**入口：** 左侧 My Apps  
**面包屑：** Home > My Apps  
**目标用户：** 账户管理员、投放运营  
**核心目标：** 管理广告主 App 资产（Bundle ID、平台、时区），查看 App 级近 7 日核心指标，控制 App 启用状态，支持新增与编辑。

---

### 11.2 筛选

| # | 字段 | 控件 | 选项/说明 |
|---|------|------|-----------|
| 1 | App Name | 文本 | 模糊匹配应用名称 |
| 2 | Platform | 下拉 | 空 / iOS / Android |
| 3 | Status | 下拉 | 空 / Active / Paused |

**页面提示：** `Spend, CVR and CPI values are calculated for the last 7 days.`

---

### 11.3 列表

| # | 列名 | 说明 |
|---|------|------|
| 1 | 勾选框 + 开关 | 批量选择；开关控制 Active/Paused |
| 2 | App Name | 图标 + 应用名称 |
| 3 | Status | Active（蓝徽章）/ Paused（灰徽章） |
| 4 | Actions | Edit + 更多菜单(▾) |
| 5 | App ID | Bundle ID，如 com.test |
| 6 | OS | iOS / Android |
| 7 | Timezone | UTC+0 / UTC+8 等 |
| 8 | Active Campaigns | 活跃 Campaign 数量 |
| 9 | Spend | 近 7 天花费，无数据显示 `--` |
| 10 | CVR | 近 7 天转化率 |
| 11 | CPI | 近 7 天单次安装成本 |
| 12 | Linked Ad Units | 关联 Ad Unit 数 |

**分页：** 10 / page  
**空态：** No apps found

---

### 11.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 页头 | + New App | 打开新建弹窗 |
| 行级 | 开关 | 切换 Active/Paused，Toast 反馈 |
| 行级 | Edit | 打开编辑弹窗，回填数据 |
| 行级 | ▾ | 更多操作（原型占位） |

**New / Edit App 弹窗字段：**

| # | 字段 | 必填 | 选项 |
|---|------|------|------|
| 1 | App Name | 是 | 文本 |
| 2 | Platform | 是 | iOS / Android |
| 3 | App ID (Bundle ID) | 是 | 文本 |
| 4 | Timezone | 否 | UTC+0 / UTC+8 / UTC-5 |

**弹窗底部：** Cancel / Save

**新建默认值：** Active=true；Spend/CVR/CPI 为 `--`；Linked Ad Units=0

---

## 12. Account Settings · Account Info

### 12.1 模块说明

**入口：** 右上角用户下拉 → Account settings → Account info  
**布局：** 独立全屏，左侧 Tab 导航  
**核心目标：** 维护账户主体信息（公司名、税号、推广网站等）。

---

### 12.2 筛选

无。

---

### 12.3 展示 / 表单字段

**头部只读信息：**

| 元素 | 说明 |
|------|------|
| 头像 | 账户名首字母缩写 |
| Account Name | 当前账户名 |
| Account ID | 只读，如 1800769483 |

**可编辑表单：**

| # | 字段 | 必填 | 控件 | 说明 |
|---|------|------|------|------|
| 1 | Account name | 否 | 文本 | 同步更新顶部用户名显示 |
| 2 | Company name | 否 | 文本 | |
| 3 | Tax ID | 否 | 文本 | 占位 Enter tax ID |
| 4 | Website to promote | 否 | 前缀 https:// + 文本 | 推广网站域名 |

---

### 12.4 操作

| 操作 | 说明 |
|------|------|
| Back | 返回进入设置前的页面 |
| Save | 保存表单，Toast 提示 |
| 左侧 Tab 切换 | Account info / Members / Payment & billing |

---

## 13. Account Settings · Members

### 13.1 模块说明

**入口：** Account settings → Members  
**核心目标：** 管理子账号（Members），查看 Owner，邀请新用户并配置权限与有效期。

---

### 13.2 筛选

| # | 字段 | 控件 | 选项 |
|---|------|------|------|
| 1 | Search users by email | 文本 | 模糊匹配邮箱 |
| 2 | Access level | 下拉 | 空 / Owner / Admin / Read-only |

---

### 13.3 列表

**Account Owner 卡片（列表上方，非表格）：**

| 元素 | 说明 |
|------|------|
| 头像 | Owner 首字母 |
| 标签 | Account owner |
| Email | Owner 邮箱 |

**All users 表格：**

| # | 列名 | 说明 |
|---|------|------|
| 1 | User email | 用户邮箱 |
| 2 | Last signed-in (UTC) | 最后登录时间 |
| 3 | Access level | Owner / Admin / Read-only 徽章 |
| 4 | Expiration (UTC) | Never / 30 days / 90 days |
| 5 | App access | All apps / Selected apps |

**分页：** 10 / page，含 Go to、Rows per page  
**空态：** No users found

---

### 13.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 工具栏 | Invite new user | 打开邀请弹窗 |

**Invite new user 弹窗：**

| # | 字段 | 必填 | 选项 |
|---|------|------|------|
| 1 | Email addresses | 是 | 文本，逗号分隔多个邮箱 |
| 2 | Access level | 否 | Read-only / Admin |
| 3 | Access expiration | 否 | Never / 30 days / 90 days |
| 4 | App access | 否 | All apps / Selected apps |

**弹窗底部：** Cancel / Invite

---

## 14. Account Settings · Payment & Billing

### 14.1 模块说明

**入口：** Account settings → Payment & billing  
**核心目标：** 查看账户余额、绑定支付方式、管理账单流水与账单地址，支撑账户充值与自动扣款。

---

### 14.2 筛选

**All billing activities 区域：**

| # | 字段 | 控件 | 说明 |
|---|------|------|------|
| 1 | Search by ID | 文本 | 按流水 ID 搜索 |
| 2 | 时间范围 | 下拉 | All time（可扩展） |
| 3 | Category | Chip 下拉 | 账单类别 |
| 4 | Status | Chip 下拉 | 账单状态 |

---

### 14.3 展示内容

#### 14.3.1 Balance 卡片

| 元素 | 说明 |
|------|------|
| Balance | 当前余额金额 |
| Added funds | 已充值金额 |
| Ad credit | 广告赠款 |
| Held for spend | 预扣花费 |

#### 14.3.2 Payment Method 卡片

| 状态 | 展示 |
|------|------|
| 无卡 | 空态图标 + No payment method added |
| 有卡 | VISA 末四位 + 过期时间 + Auto reload 标签 |

#### 14.3.3 All billing activities 表格

| # | 列名 |
|---|------|
| 1 | ID |
| 2 | Time (UTC) |
| 3 | Category |
| 4 | Description |
| 5 | Amount |
| 6 | Payment method |
| 7 | Status |

**分页：** 50 / page

#### 14.3.4 Billing Address 卡片

| 元素 | 说明 |
|------|------|
| 地址文本 | 已填地址或 `—` |

---

### 14.4 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| Balance 卡片 | Add funds | 充值（原型 Toast 占位） |
| Payment Method | Add payment method | 打开 Add Card 弹窗 |
| Billing activities | Receive receipt emails | 开关，控制是否接收邮件收据 |
| Billing activities | Filter | 高级筛选（原型占位） |
| Billing activities | Export data ▾ | 导出流水 |
| Billing Address | Add / Edit billing information | 打开账单地址弹窗 |

**Add Card 弹窗：**

| 区块 | 字段 |
|------|------|
| Card details | Card number, Expiry date, CVV |
| Billing address | Full name, Country or region, Postal code |
| 底部 | Cancel / Save |

**Add Billing Information 弹窗：**

| 元素 | 说明 |
|------|------|
| 提示 | 提交后不可修改 |
| 字段 | Full name, Country or region, Postal code |
| 底部 | Cancel / Save；Powered by Stripe |

---

## 15. 附录

### 附录 A：报表指标字段库

广告报表与素材报表 **Configure Columns** 共用以下字段分组。日维度字段统一延伸至 **D30**（序列：D0, D1, D3, D7, D14, D28, D30）。

#### A.1 Basic

Impressions, Clicks, CTR, Installs, CPI, IR, Spend, **Revenue**, **ROI**, CPM

#### A.2 Campaign goals

CPP goal, CPE goal, ROAS goal

#### A.3 Retention

D1 / D3 / D7 / D14 / D28 / D30 retention

#### A.4 IAA revenue

- D0–D30 IAA rev、1Y IAA rev  
- D0–D30 IAA ROAS

#### A.5 IAP revenue

- D0–D30 IAP rev、1Y IAP rev  
- D0–D30 IAP ROAS

#### A.6 Total revenue

- D0–D30 total rev、1Y total rev  
- D0–D30 total ROAS、D60 / D90 / D180 total ROAS

#### A.7 Purchases

- D0–D30 unique purchasers、Unique purchasers  
- D0–D30 CPP

#### A.8 Target events

- D0–D30 unique target events、1Y unique target events  
- D0–D30 target event CPE

---

### 附录 B：页面路由对照

| 模块 | 页面 | page 标识 |
|------|------|-----------|
| Dashboard | Overview | `dashboard-overview` |
| Dashboard | Risk Control | `dashboard-risk` |
| Ads | Ads | `ads` |
| Creative | Manage Creative | `manage-creative` |
| Creative | 素材表单子页 | `manage-creative` + `mcView=asset-form` |
| Reports | Download Reports | `reports-download` |
| Reports | Ad Monitoring | `reports-ad-monitor` |
| Reports | Creative Monitoring | `reports-creative-monitor` |
| Apps | My Apps | `apps` |
| Settings | Account Settings | `account-settings` |

---

### 附录 C：待后续迭代

| 类别 | 项 |
|------|-----|
| 后端 | 全部模块 API 对接、鉴权、权限控制 |
| 路由 | Vue Router URL 化 |
| 国际化 | 完整 i18n |
| Dashboard | Overview 图表真实数据 |
| Reports | 异步报表生成与下载 |
| Billing | Stripe 真实支付 |
| Members | App 级细粒度权限勾选 |
| 移动端 | 响应式适配 |

---

*文档结束*
