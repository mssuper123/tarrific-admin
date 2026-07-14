# tarrific.tv DSP Admin 产品需求文档

> **版本：** v1.2  
> **更新日期：** 2026-07-14  
> **文档状态：** 基于交互原型（`index.html`）整理  
> **对标：** AppLovin（AXON Ads Manager）广告主后台能力与信息架构  
> **适用对象：** 产品、设计、前端、后端、测试、运营

> **相关专项文档：** [Risk Control 说明文档](./RISK_CONTROL.md)（风控看板完整口径）

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

> **入口：** 打开 `index.html` / 本地静态服务  
> **页面标识：** 由 `page` 变量控制  
> **适用对象：** 全角色

### 1.1 模块定位

tarrific.tv DSP 管理后台，面向广告主及代理商运营，提供：

1. 投放效果与风险监控（Dashboard）  
2. Campaign / Ad Group 管理（Ads）  
3. 素材组与素材管理（Manage Creative）  
4. 明细报表与离线下载（Reports）  
5. App 资产与账户成员 / 计费（My Apps、Account Settings）

当前为单页 Vue 3 CDN 原型，无 URL 路由、无真实 API。

### 1.2 页面结构（整体布局）

```
┌──────────┬─────────────────────────────────────┐
│ Sidebar  │ Topbar（语言 / 用户下拉）            │
│ 200px    ├─────────────────────────────────────┤
│ 导航     │ Content（面包屑 + 标题 + 业务）      │
│          │                                     │
└──────────┴─────────────────────────────────────┘
```

| 区域 | 说明 |
|------|------|
| 左侧导航 | 深色主题；一级菜单 + 可展开子菜单 |
| 顶部栏 | 语言（预留 i18n）、用户头像与名称下拉 |
| 主内容区 | 各业务页面；Account Settings 为独立全屏布局 |

### 1.3 导航菜单

| 一级 | 子菜单 | page 标识 | 默认 |
|------|--------|-----------|------|
| Dashboard | Overview | `dashboard-overview` | ✓ |
| Dashboard | Risk Control | `dashboard-risk` | |
| Ads | — | `ads` | |
| Manage Creative | — | `manage-creative` | |
| Reports | Download Reports | `reports-download` | |
| Reports | Ad Monitoring Report | `reports-ad-monitor` | |
| Reports | Creative Monitoring Report | `reports-creative-monitor` | |
| My Apps | — | `apps` | |

> Account Settings **不在侧栏**，入口：右上角用户下拉 → **Account settings**（`account-settings`）。

### 1.4 顶部栏操作

| 元素 | 说明 |
|------|------|
| 语言 | 静态 English，预留 i18n |
| 用户头像 + 名称 | 点击展开下拉 |
| Account settings | 进入账户设置 |
| Sign out | 退出（原型 Toast） |

### 1.5 通用规范

| 规范项 | 说明 |
|--------|------|
| 主色 | `#5bc0eb` |
| 反馈 | Toast 成功/失败提示 |
| 分页 | Total、‹ ›、页码、每页条数 |
| 空态 | 居中文案 / 图标 |
| 模态框 | 标题 + 关闭(X) + Cancel + 主按钮 |
| 状态色 | `good` 绿 / `warn` 橙 / `bad` 红（与 Risk Control 一致） |

### 1.6 Overview 与 Risk Control 分工

| | Overview | Risk Control |
|--|----------|--------------|
| 视角 | 效果看数（Spend / Revenue / eCPI / AU…） | 风险与干预（预算、目标、审核、告警） |
| 典型动作 | 分析趋势、下钻报表 | 发现异常 → 跳转 Ads / Creative / Billing |
| 专项文档 | 本文 §2 | [RISK_CONTROL.md](./RISK_CONTROL.md) |

---

## 2. Dashboard · Overview

> **入口：** Dashboard → Overview  
> **页面标识：** `dashboard-overview`  
> **面包屑：** Home > Dashboard  
> **对标：** AppLovin 账户级效果总览  
> **适用对象：** 运营 / 投放

### 2.1 模块定位

按 **App + 时间范围** 查看账户级核心投放指标（Spend / Revenue / ROI 优先），并通过趋势图与明细表下钻。

本页为 **只读看板**，无增删改；数据来自报表聚合（原型 Mock）。

### 2.2 页面结构

```
筛选条（App / Date Range / Timezone）
    ↓
KPI 卡片区（13 个，7 列 × 2 排）
    ↓
趋势图占位
    ↓
明细表（Group by + Chart/Table 切换）
```

### 2.3 筛选

| 字段 | 说明 | 影响范围 |
|------|------|----------|
| App | 应用选择 | KPI、图表、明细表 |
| Date Range | 统计区间（原型文本框） | 同上 |
| Timezone | UTC+0 / UTC+8 / UTC-5 | 预留对齐「日」边界 |

### 2.4 KPI 卡片（13）

优先顺序：Spend → Revenue → ROI 相邻前置。

| # | 字段 | 格式示例 | 说明 |
|---|------|----------|------|
| 1 | Spend | $0 | 花费 |
| 2 | Revenue | $0 | 收入 |
| 3 | ROI | 0% | 投资回报率 |
| 4 | Installs | 0 | 安装量 |
| 5 | Impressions | 0 | 展示 |
| 6 | Clicks | 0 | 点击 |
| 7 | eCPI | $0 | 有效单次安装成本 |
| 8 | eCPM | $0 | 有效千次展示成本 |
| 9 | CTR | 0% | 点击率 |
| 10 | eCPC | $0 | 有效单次点击成本 |
| 11 | D0 AU | 0 | 安装日活跃用户数（Active Users） |
| 12 | D7 AU | 0 | 第 7 日活跃用户数 |
| 13 | D30 AU | 0 | 第 30 日活跃用户数 |

> **AU** = Active Users（活跃用户），非 DAU（日历日活）。展示为**数量**，不是留存率 %。与 Reports「Active users」分组字段对齐。

### 2.5 图表区

| 元素 | 说明 |
|------|------|
| 趋势图 | 核心指标随时间变化（原型空态） |
| 空态 | 飞机图标 + No data |

### 2.6 明细表

**Group by（三选一，决定首列）：** Date / Campaign / Ad Group  

**数据列顺序：** Spend → Revenue → ROI → Installs → Impressions → Clicks → eCPI → eCPM → CTR → eCPC → D0 AU → D7 AU → D30 AU  

**操作：** Chart / Table 视图切换图标  

**空态：** No data  

### 2.7 操作

| 操作 | 说明 |
|------|------|
| Chart / Table 切换 | 切换展示模式 |
| — | 无增删改（只读） |

### 2.8 原型边界

- KPI / 表数据均为 Mock `$0` / 空表  
- 图表未接真实序列数据  

---

## 3. Dashboard · Risk Control

> **入口：** Dashboard → Risk Control  
> **页面标识：** `dashboard-risk`  
> **对标：** AppLovin 广告主「预算 pacing + Goal + 素材审核」盯盘  
> **完整说明：** 见专项文档 [RISK_CONTROL.md](./RISK_CONTROL.md)

### 3.1 模块定位（摘要）

账户级风险与健康监控，回答：

1. 钱够不够、还能花几天？  
2. Campaign 预算超花还是花不完？  
3. CPI / CPP / ROAS 目标是否达标？  
4. 素材审核是否卡住 / 被拒？  

### 3.2 页面结构

```
筛选（App / Date / Timezone）+ Refresh
    ↓
Account Health（Score + Tags）
    ↓
KPI × 4（Balance / Off Goal / Pending Review / Burn Days）
    ↓
Budget Progress  |  Delivery Health
    ↓
Campaign Budget Status（含 Goal / Actual / Goal Status）
    ↓
Creative Review Queue  |  Risk Alerts（过滤 + View / Mute）
```

### 3.3 筛选与操作（摘要）

| 项 | 说明 |
|----|------|
| App | 过滤 Campaign 表 |
| Date / Timezone | 预留统计口径 |
| Refresh | 刷新并显示 Updated 时间戳 |
| Alerts View | 跳转 Ads / Manage Creative / Reports / Billing |
| Alerts Mute 24h | 原型会话内隐藏 |

### 3.4 字段与字典

状态字典（Status / Pacing / Goal Status）、Delivery Health 指标解读、告警级别与跳转映射等，**以 [RISK_CONTROL.md](./RISK_CONTROL.md) 为准**，本文不重复展开。

---

## 4. Ads

> **入口：** Ads  
> **页面标识：** `ads`  
> **面包屑：** Home > Ads  
> **对标：** AppLovin Campaign / Ad Group 列表  
> **适用对象：** 投放运营

### 4.1 模块定位

管理 Campaign 与 Ad Group 的启用状态，查看基础信息与时间戳；支持新建 / 编辑入口（部分能力原型占位）。

### 4.2 页面结构

```
筛选栏（+ Filter / Date Range）
    ↓
Tab：Campaign | Ad Group
    ↓
工具栏（New / Edit / Show Metrics / Custom Columns / Refresh）
    ↓
列表表 + 分页
```

### 4.3 筛选

| 字段 | 说明 |
|------|------|
| + Filter | 高级筛选（原型占位） |
| Date Range | 如 `2026-07-03 ~ 2026-07-09`，配合 Show Metrics |

**Tab：**

| Tab | Name 列标题 |
|-----|-------------|
| Campaign | Campaign Name |
| Ad Group | Ad Group Name |

### 4.4 列表

| 列 | 说明 |
|----|------|
| 勾选框 | 批量选择 |
| 开关 | Active / Paused |
| Name | Campaign 或 Ad Group 名称 |
| ID | 唯一标识 |
| Status | Active（绿点）/ Paused（灰点） |
| Updated Time | 最后更新 |
| Created Time | 创建时间 |

**分页：** 10 / page  

### 4.5 操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 工具栏 | New | 新建（原型占位） |
| 工具栏 | Edit ▾ | 需勾选一条，否则 disabled |
| 工具栏 | Show Metrics | 显示指标列（占位） |
| 工具栏 | Custom Columns | 自定义列（占位） |
| 工具栏 | ↻ Refresh | 刷新列表 |
| 行级 | 开关 | 切换 Active / Paused |

### 4.6 原型边界

New / Edit / Metrics / Custom Columns 多为占位，未接完整表单与 API。

---

## 5. Manage Creative · Creative Group

> **入口：** Manage Creative → Tab Creative Group  
> **页面标识：** `manage-creative` + `mcTab=group`  
> **对标：** AppLovin Creative Set / 素材组合管理  
> **适用对象：** 素材运营

### 5.1 模块定位

管理 Creative Group：将多种素材类型组合并关联 Campaign / Ad Group，支持新建、编辑、复制、删除。

### 5.2 页面结构

```
Tab：Creative Group | Creatives
    ↓
筛选（Name / Combination Method / Ad Unit）+ New Creative Group
    ↓
列表表 + 分页
    ↓
弹窗：New/Edit Creative Group
    ↓（Upload）批量上传弹窗
    ↓（Add Existing）选择已有 Creative 子弹窗
```

### 5.3 筛选

| 字段 | 控件 | 说明 |
|------|------|------|
| Creative Group Name | 文本 | 模糊匹配名称 |
| Combination Method | 下拉 | 空 / Programmatic Creative / Manual Combination |
| Ad Unit Name | 文本 | 模糊匹配 Ad Unit |

输入后列表实时过滤（`filteredGroups`）。

### 5.4 列表

| 列 | 说明 |
|----|------|
| 勾选框 | 批量选择 |
| ID | 素材组 ID |
| Creative Group Name | 名称 |
| Campaign Name / ID | 关联 Campaign |
| Ad Group Name / ID | 关联 Ad Group |
| Combination Method | Programmatic / Manual |
| Creative Type | 多类型标签 |
| Deliverable Ad Types | 可投广告类型 |
| Country | 国家，默认 ALL |
| Ad Unit | Ad Unit |
| Created Time | 创建时间 |
| Actions | Edit / Copy / Delete |

**分页：** 20 / page  
**空态：** No creative groups found  

### 5.5 行级 / 页级操作

| 操作 | 说明 |
|------|------|
| New Creative Group | 打开新建弹窗 |
| Edit | 回填编辑 |
| Copy | 复制，名称追加 `(Copy)` |
| Delete | 删除单条，Toast 确认 |

### 5.6 新建 / 编辑弹窗

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| Creative Group Name | 是 | 文本 | 如 S1S2nativevideo |
| Creative Type | 是（至少 1） | **多选 Checkbox** | Banner / Native Image / Video / Video EndCard / Playable |
| Creatives | 是（至少 1） | 表格 + 工具栏 | 见下 |
| Country | 否 | 文本 | 默认 ALL |

> 文案统一使用 **Creatives**（不再使用 Materials）。

**Creatives 工具栏：**

| 操作 | 说明 |
|------|------|
| Upload | 打开**批量拖拽上传弹窗**（见 §5.7） |
| Add Existing Creative | 从已有 Creatives 多选添加 |
| Delete Selected | 删除选中行 |

**Creatives 表列：** 勾选 · Creative ID · Asset Name · Type · Preview · Resolution · Source（Upload / Existing）· Upload Time · Status  

**底部：** Cancel / Save Creative Group  

**校验：** 名称非空；至少选一个 Creative Type；至少一条 Creative。

### 5.7 批量上传弹窗（Upload）

样式与主色对齐全站 `#5bc0eb`。

```
标题栏：Upload Images or Videos
    ↓
拖拽 / 点击上传区（多文件）
    ↓
Upload Progress
    ↓
Uploaded Creatives 表（Creative Name / Type / Resolution / Upload Time）
    ↓
分页
    ↓
Cancel / Submit
```

| 操作 | 说明 |
|------|------|
| 拖拽或点击 | 支持 image / video / html / zip，多文件 |
| Submit | 写入父弹窗 Creatives 表后关闭 |
| Cancel | 丢弃本次上传列表 |

### 5.8 Add Existing Creative 子弹窗

| 元素 | 说明 |
|------|------|
| 搜索 | 按名称或 ID |
| 列表 | 多选尚未关联的 Creative |
| Cancel / Add Selected | 确认添加 |

### 5.9 原型边界

- 上传为本地 FileReader 预览，无真实 OSS  
- Combination Method 与投放实配逻辑未接后端  

---

## 6. Manage Creative · Creatives

> **入口：** Manage Creative → Tab Creatives  
> **页面标识：** `manage-creative` + `mcTab=creatives`  
> **适用对象：** 素材运营

### 6.1 模块定位

管理单条 Creative（Asset）：查看关联 Campaign / Ad Group，控制启用，支持新建与编辑。

### 6.2 页面结构

```
工具栏（New / Edit / Show Metrics / Custom Columns / Refresh）
    ↓
Creative 列表 + 分页
```

### 6.3 筛选

当前无独立筛选栏，全量展示。

### 6.4 列表

| 列 | 说明 |
|----|------|
| 勾选框 | 批量选择 |
| 开关 | Active / Paused |
| Creative Name / ID | 名称与 ID |
| Creative Type | 类型标签 |
| Campaign Name / ID | |
| Ad Group Name / ID | |
| Status | Active / Paused |
| Updated / Created Time | |
| Schedule | 如 Always |

**分页：** 10 / page  

### 6.5 操作

| 操作 | 说明 |
|------|------|
| New | 进入新建素材页（`mcView=asset-form`） |
| Edit ▾ | 需恰好勾选 1 条 |
| Show Metrics / Custom Columns | 占位 |
| ↻ Refresh | Toast |
| 行开关 | 切换启用，Toast |

---

## 7. Manage Creative · 新建/编辑素材页

> **入口：** Creatives → New / Edit  
> **页面标识：** `manage-creative` + `mcView=asset-form`  
> **面包屑：** Home > Manage Creative > New Asset / Edit Asset  

### 7.1 模块定位

上传素材、填写文案、选择类型，并实时预览移动端广告效果；可选关联 Creative Group。

### 7.2 页面结构

```
表单（Type / Upload / Title / Description / CTA） | 手机预览
    ↓
Apply creatives to existing Creative Group（可选）
    ↓
Back to List / Save
```

### 7.3 表单字段

| 字段 | 控件 | 说明 |
|------|------|------|
| Creative Type | 分段按钮 | Banner / Native Image / Video / Video EndCard / Playable |
| 上传区 | 拖拽 / 点击 | 图片或视频，单次 1 个 |
| Title | 文本 | 广告标题 |
| Description | 多行 | 描述 |
| Native CTA Text | 文本 | 如 Install |

**Apply to Creative Group：** Yes/No；筛选 Name / Ad Unit + Find；勾选目标 Group。

### 7.4 操作

| 操作 | 说明 |
|------|------|
| Back to List | 返回 Creatives 列表 |
| Save | 新建或更新后返回列表 |

---

## 8. Reports · Download Reports

> **入口：** Reports → Download Reports  
> **页面标识：** `reports-download`  

### 8.1 模块定位

查看已生成的离线报表任务，支持搜索、下载、查看、删除。

### 8.2 页面结构

```
页头 + Create
    ↓
Search
    ↓
报表任务列表 + 分页
```

### 8.3 筛选

| 字段 | 说明 |
|------|------|
| Search | 匹配 Report name 或 Create by（不区分大小写） |

### 8.4 列表

| 列 | 说明 |
|----|------|
| Report name | 报表名称 |
| Time range | 统计时间范围 |
| Rows/size | 行数 / 文件大小 |
| Status | 如 Available for download until yyyy-mm-dd |
| Create date / Create by | 创建信息 |
| Actions | Download / View / Delete |

**分页：** 10 / page  
**空态：** No reports found  

### 8.5 操作

| 操作 | 说明 |
|------|------|
| + Create | 创建任务（原型 Toast 占位） |
| Download / View / Delete | 行级操作 |

---

## 9. Reports · Ad Monitoring Report

> **入口：** Reports → Ad Monitoring Report  
> **页面标识：** `reports-ad-monitor`  
> **对标：** AppLovin 明细报表 + Columns 配置  

### 9.1 模块定位

按多维条件查询广告层级投放效果，自定义维度与指标列，支持导出。

### 9.2 页面结构

```
查询条件网格（12 字段）
    ↓
Data Dimensions（checkbox）
    ↓
Data Indicators 摘要 + Configure Columns
    ↓
Search Data / Export Data
    ↓
结果表（可排序）+ 分页
```

### 9.3 筛选（查询条件）

| # | 字段 | 控件 |
|---|------|------|
| 1 | Timezone | 下拉 |
| 2 | Date Range | 文本 |
| 3–4 | Campaign Name / ID | 文本 |
| 5–6 | Ad Group Name / ID | 文本 |
| 7–8 | Creative Name / ID | 文本 |
| 9–10 | Creative Group Name / ID | 文本 |
| 11 | Creative Type | 下拉 |
| 12 | Delivery Region | 文本 |

### 9.4 Data Dimensions（默认）

默认勾选：Day · Campaign Name · Ad Group Name  

可选：Hour / Week / Month / Campaign ID / Ad Group ID / Creative Group Name|ID / Creative Name|ID / Creative Type / Country · Region  

### 9.5 Data Indicators

通过 **Configure Columns** 配置。默认选中：

`Impressions, Clicks, CTR, Installs, CPI, IR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS`

完整字段库见 [附录 A](#附录-a报表指标字段库)。

> 已明确 **移除**：Advanced Options、CSV Batch Block Traffic。

### 9.6 结果列表

列 = 已选 Dimensions + 已选 Indicators；列头可排序；分页 50 / page；空态 No data。

### 9.7 操作

| 操作 | 说明 |
|------|------|
| Search Data | 查询，Toast 返回条数 |
| Export Data | 导出当前结果 |
| Configure Columns | 打开列配置（搜索 / Add·Remove / Add all·Remove all / Apply） |

---

## 10. Reports · Creative Monitoring Report

> **入口：** Reports → Creative Monitoring Report  
> **页面标识：** `reports-creative-monitor`  

### 10.1 模块定位

与广告报表结构一致，默认以**素材维度**为主分析。

### 10.2 与广告报表差异

| 项 | Ad Monitoring | Creative Monitoring |
|----|---------------|---------------------|
| 查询条件 / 指标库 / 操作 | 相同 | 相同 |
| Dimensions 默认 | Day · Campaign · Ad Group | Day · Ad Group · Creative Name · Creative ID |

其余规则同 §9。

---

## 11. My Apps

> **入口：** My Apps  
> **页面标识：** `apps`  
> **面包屑：** Home > My Apps  

### 11.1 模块定位

管理 App 资产（Bundle ID、平台、时区），查看近 7 日核心指标，控制启用，支持新增与编辑。

### 11.2 页面结构

```
页头 + New App
    ↓
筛选（App Name / Platform / Status）
    ↓
提示：Spend/CVR/CPI 为近 7 日
    ↓
App 列表 + 分页
    ↓
New / Edit App 弹窗
```

### 11.3 筛选

| 字段 | 选项 |
|------|------|
| App Name | 模糊匹配 |
| Platform | 空 / iOS / Android |
| Status | 空 / Active / Paused |

### 11.4 列表

| 列 | 说明 |
|----|------|
| 勾选 + 开关 | 批量选择；Active / Paused |
| App Name | 图标 + 名称 |
| Status | Active / Paused 徽章 |
| Actions | Edit + ▾（更多占位） |
| App ID | Bundle ID |
| OS / Timezone | |
| Active Campaigns | 活跃 Campaign 数 |
| Spend / CVR / CPI | 近 7 日，无数据为 `--`；可排序 |
| Linked Ad Units | 关联 Ad Unit 数 |

**分页：** 10 / page  
**空态：** No apps found  

### 11.5 操作与弹窗

| 操作 | 说明 |
|------|------|
| + New App | 新建弹窗 |
| 行开关 | Active / Paused，Toast |
| Edit | 回填编辑 |

**弹窗字段：** App Name* · Platform*（iOS/Android）· App ID* · Timezone  

**新建默认：** Active=true；Spend/CVR/CPI=`--`；Linked Ad Units=0  

---

## 12. Account Settings · Account Info

> **入口：** 用户下拉 → Account settings → Account info  
> **页面标识：** `account-settings` + `settingsTab=account-info`  

### 12.1 模块定位

维护账户主体信息（账户名、公司、税号、推广网站）。

### 12.2 页面结构

```
独立全屏：返回 + 左侧 Tab（Account info / Members / Payment & billing）
    ↓
头像 / Account Name / Account ID（只读）
    ↓
可编辑表单 + Save
```

### 12.3 表单字段

| 字段 | 说明 |
|------|------|
| Account name | 同步顶部用户名显示 |
| Company name | |
| Tax ID | |
| Website to promote | 前缀 `https://` + 域名 |

### 12.4 操作

| 操作 | 说明 |
|------|------|
| Back | 返回进入设置前的页面 |
| Save | 保存，Toast |
| 左侧 Tab | 切换三个设置子页 |

---

## 13. Account Settings · Members

> **入口：** Account settings → Members  
> **Tab：** `members`  

### 13.1 模块定位

管理子账号：查看 Owner、邀请用户并配置权限与有效期。

### 13.2 页面结构

```
筛选（email / Access level）+ Invite new user
    ↓
Account Owner 卡片
    ↓
All users 表 + 分页
    ↓
Invite 弹窗
```

### 13.3 筛选

| 字段 | 说明 |
|------|------|
| Search users by email | 模糊匹配邮箱 |
| Access level | 空 / Owner / Admin / Read-only |

### 13.4 展示

**Owner 卡片：** 头像、Account owner 标签、Email  

**用户表列：** User email · Last signed-in (UTC) · Access level · Expiration (UTC) · App access  

**分页：** 10 / page，含 Go to、Rows per page  

### 13.5 Invite 弹窗

| 字段 | 选项 |
|------|------|
| Email addresses* | 逗号分隔多邮箱 |
| Access level | Read-only / Admin |
| Access expiration | Never / 30 days / 90 days |
| App access | All apps / Selected apps |

底部：Cancel / Invite  

---

## 14. Account Settings · Payment & Billing

> **入口：** Account settings → Payment & billing  
> **Tab：** `billing`  

### 14.1 模块定位

查看余额、支付方式、账单流水与账单地址，支撑充值与自动扣款。

### 14.2 页面结构

```
Balance 卡片 | Payment Method 卡片
    ↓
All billing activities（筛选 + 表）
    ↓
Billing Address 卡片
    ↓
Add Card / Add Billing Information 弹窗
```

### 14.3 筛选（账单流水）

| 字段 | 说明 |
|------|------|
| Search by ID | 按流水 ID |
| 时间范围 | All time（可扩展） |
| Category / Status | Chip 下拉 |

### 14.4 展示

**Balance：** Balance · Added funds · Ad credit · Held for spend  

**Payment Method：** 无卡空态 / 有卡展示 VISA 末四位 + 过期 + Auto reload  

**流水表列：** ID · Time (UTC) · Category · Description · Amount · Payment method · Status；分页 50 / page  

**Billing Address：** 已填地址或 `—`  

### 14.5 操作

| 操作 | 说明 |
|------|------|
| Add funds | 充值（Toast 占位） |
| Add payment method | Add Card 弹窗 |
| Receive receipt emails | 开关 |
| Filter / Export data | 占位 / 导出 |
| Add / Edit billing information | 账单地址弹窗 |

**Add Card：** Card number / Expiry / CVV + Billing address 基础字段 · Cancel / Save  

**Add Billing Information：** 提示提交后不可改；Full name / Country / Postal · Powered by Stripe  

---

## 15. 附录

### 附录 A：报表指标字段库

广告报表与素材报表 **Configure Columns** 共用。日维度序列：**D0, D1, D3, D7, D14, D28, D30**。

#### A.1 Basic

Impressions, Clicks, CTR, Installs, CPI, **eCPI**, IR, Spend, Revenue, ROI, CPM, **eCPM**, **eCPC**

#### A.2 Campaign goals

CPP goal, CPE goal, ROAS goal

#### A.3 Active users

**D0 AU, D7 AU, D30 AU**（与 Overview 看板对齐）

#### A.4 Retention

D1 / D3 / D7 / D14 / D28 / D30 retention

#### A.5 IAA revenue

D0–D30 IAA rev、1Y IAA rev；D0–D30 IAA ROAS

#### A.6 IAP revenue

D0–D30 IAP rev、1Y IAP rev；D0–D30 IAP ROAS

#### A.7 Total revenue

D0–D30 total rev、1Y total rev；D0–D30 / D60 / D90 / D180 total ROAS

#### A.8 Purchases

D0–D30 unique purchasers、Unique purchasers；D0–D30 CPP

#### A.9 Target events

D0–D30 unique target events、1Y unique target events；D0–D30 target event CPE

---

### 附录 B：页面路由对照

| 模块 | 页面 | page 标识 |
|------|------|-----------|
| Dashboard | Overview | `dashboard-overview` |
| Dashboard | Risk Control | `dashboard-risk` |
| Ads | Ads | `ads` |
| Creative | 列表 | `manage-creative` |
| Creative | 素材表单 | `manage-creative` + `mcView=asset-form` |
| Reports | Download | `reports-download` |
| Reports | Ad Monitoring | `reports-ad-monitor` |
| Reports | Creative Monitoring | `reports-creative-monitor` |
| Apps | My Apps | `apps` |
| Settings | Account Settings | `account-settings` |

---

### 附录 C：跨模块指标对齐

| 指标 | Overview | Risk Control | Reports |
|------|----------|--------------|---------|
| Spend / Revenue / ROI | ✓ | —（间接看预算） | ✓ Basic |
| eCPI / eCPM / eCPC | ✓ | eCPI Spike / Campaign Actual | ✓ Basic |
| D0/D7/D30 AU | ✓ | — | ✓ Active users |
| CPI / CPM | —（看板用 e*） | Goal 可用 CPI | ✓ 并存 |
| Goal / Pacing / Review | — | ✓ 主战场 | — |

---

### 附录 D：待后续迭代

| 类别 | 项 |
|------|-----|
| 后端 | API、鉴权、权限 |
| 路由 | Vue Router URL 化 |
| i18n | 完整多语言 |
| Overview | 图表真实数据 |
| Risk | Goal Matrix、Alert Rules、Ad Group 下钻（见 RISK_CONTROL §15） |
| Reports | 异步生成与下载 |
| Billing | Stripe 真实支付 |
| Members | App 级细粒度权限 |
| 移动端 | 响应式适配 |

---

### 附录 E：文档索引

| 文档 | 内容 |
|------|------|
| [REQUIREMENTS.md](./REQUIREMENTS.md) | 全模块产品需求（本文） |
| [RISK_CONTROL.md](./RISK_CONTROL.md) | Risk Control 专项说明（字段字典、告警跳转、运营读盘顺序） |

---

*文档结束 · v1.2*
