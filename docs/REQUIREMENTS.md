# tarrific.tv DSP Admin 产品需求文档

> **版本：** v1.11  
> **更新日期：** 2026-07-16  
> **文档状态：** 基于交互原型（`index.html`）整理  
> **对标：** AppLovin（AXON Ads Manager）广告主后台能力与信息架构  
> **适用对象：** 产品、设计、前端、后端、测试、运营

> **相关专项文档：** [Risk Control 说明文档](./RISK_CONTROL.md)（风控看板完整口径）

### 更新记录

| 版本 | 日期 | 变更摘要 |
|------|------|----------|
| v1.11 | 2026-07-16 | Targeting Settings 新增 Events 子菜单；My Apps 支持 Events 快捷配置 |
| v1.10 | 2026-07-16 | My Apps 新建/编辑弹窗支持 Daily Available Budget 与 No budget limit |
| v1.9 | 2026-07-16 | Targeting Settings 拆为 Allow & Block + Data Packages 子菜单；数据包 Country/Platform/Device Type 联动与搜索选择器；黑白名单模块重命名 Allow & Block，列表 App ID 列与 My Apps 统一 |
| v1.8 | 2026-07-16 | 新增 Targeting Settings 模块（按 App 配置黑白名单）；Ads Step 2 支持 Ad Group 级黑白名单；My Apps 快捷入口 |
| v1.7 | 2026-07-15 | Ads Campaign / Ad Group 列表列对齐 AppLovin（含 Show Metrics 指标列） |
| v1.6 | 2026-07-15 | Ad Group 向导 Step 2 仅新建；Ad / Creative Monitoring 移除 Week 维度 |
| v1.5 | 2026-07-14 | Creative Group 列表精简列；Members Invite 改为 Password；My Apps 新增 Icon / Preview Link |
| v1.4 | 2026-07-14 | Ads 三步新建向导（Campaign / Ad Group / Creative）完整原型说明 |
| v1.3 | 2026-07-14 | Ad Monitoring 移除素材筛选项/维度；报表 IR→IVR；两报表分工说明补全 |
| v1.2 | 2026-07-14 | 按 Risk 文档格式重写全模块；Overview AU / 报表 eCPI·eCPM·eCPC；Creative Group 多选框与批量上传 |
| v1.1 | 2026-07-13 | 按模块细化筛选、列表、操作 |
| v1.0 | 2026-07-13 | 初版模块总览 |

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
12. [Targeting Settings](#12-targeting-settings)
13. [Account Settings · Account Info](#13-account-settings--account-info)
14. [Account Settings · Members](#14-account-settings--members)
15. [Account Settings · Payment & Billing](#15-account-settings--payment--billing)
16. [附录](#16-附录)
    - [附录 A：报表指标字段库](#附录-a报表指标字段库)
    - [附录 F：广告报表 vs 素材报表对照](#附录-f广告报表-vs-素材报表对照)

---

## 1. 全局框架

> **入口：** 打开 `index.html` / 本地静态服务  
> **页面标识：** 由 `page` 变量控制  
> **适用对象：** 全角色

### 1.1 模块定位

tarrific.tv DSP 管理后台，面向广告主及代理商运营，提供：

1. 投放效果与风险监控（Dashboard）  
2. Campaign / Ad Group 管理（Ads）— 列表 + 三步新建向导  
3. 素材组与素材管理（Manage Creative）  
4. 明细报表与离线下载（Reports）  
5. App 资产、定向设置与账户成员 / 计费（My Apps、Targeting Settings、Account Settings）

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
| Ads | — | `ads`（列表 `adsView=list`；新建 `adsView=wizard`） | |
| Targeting Settings | Allow & Block | `targeting-list` | ✓（子菜单默认） |
| Targeting Settings | Data Packages | `targeting-data-packages` | |
| Targeting Settings | Events | `targeting-events` | |
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
> **视图状态：** `adsView` = `list` | `wizard`  
> **面包屑：** Home > Ads（列表）；新建向导为全屏覆盖层（× New）  
> **对标：** AppLovin Campaign / Ad Group 列表 + 创建向导  
> **适用对象：** 投放运营

### 4.1 模块定位

管理 Campaign 与 Ad Group 的启用状态、基础信息与时间戳；支持 **三步新建向导**（Campaign → Ad Group → Creative）。**Step 1（Campaign）** 与 **Step 3（Creative）** 支持 **New** / **Select Existing（History Data）**；**Step 2（Ad Group）** 直接进入新建表单，无模式切换。

### 4.2 页面结构

**列表视图（`adsView=list`）**

```
筛选栏（+ Filter / Date Range）
    ↓
Tab：Campaign | Ad Group
    ↓
工具栏（New / Edit / Show Metrics / Custom Columns / Refresh）
    ↓
列表表 + 分页
```

**新建向导（`adsView=wizard`）**

```
顶部：× New
    ↓
左侧步骤条（可点击跳转）：1 Campaign → 2 Ad Group → 3 Creative
    ↓
右侧内容区：当前步骤表单
    ↓
右上：Back to List | Next Step（Step 1–2）/ Save（Step 3）
```

| 状态字段 | 说明 |
|----------|------|
| `adsWizardStep` | `1` Campaign · `2` Ad Group · `3` Creative |
| `adsCampaignForm` | Step 1 表单 |
| `adsAdGroupForm` | Step 2 表单 |
| `adsCreativeForm` | Step 3 模式与创意类型 |
| `adsCampaignData` / `adsAdGroupData` | 列表 Mock 数据（Campaign / Ad Group 分表） |
| `adsShowMetrics` | 列表是否显示指标列，默认 `true` |
| `targetingSettingsData` | Allow & Block Mock 数据 |
| `targetingModal` | Allow & Block 弹窗状态 |
| `dataPackages` | Data Packages Mock 数据 |
| `adsAdGroupForm.dataPackageIds` | Step 2 已选数据包 ID |

### 4.3 列表页

#### 4.3.1 筛选

| 字段 | 说明 |
|------|------|
| + Filter | 高级筛选（原型占位） |
| Date Range | 如 `2026-07-03 ~ 2026-07-09`，配合 Show Metrics |

**Tab：**

| Tab | 数据源 | 固定列摘要 |
|-----|--------|------------|
| Campaign | `adsCampaignData` | Name · ID · Status · Budget · Created/Updated · Objective |
| Ad Group | `adsAdGroupData` | Name · ID · Campaign · Region · Status · Schedule · Bid · Campaign Budget |

**状态：** `adsShowMetrics`（默认 `true`）控制 Spend / Impressions 等指标列显隐。

#### 4.3.2 列表列

**Campaign Tab**

| 列 | 说明 |
|----|------|
| 勾选框 / 开关 | 批量选择；Active / Paused |
| Campaign Name / Campaign ID | 名称与 ID |
| Status | Active / Paused |
| Budget | 如 `0 USD` |
| Created Time / Updated Time | `YYYY-MM-DD HH:MM:SS` |
| Objective | APP Promote / Web Promote |
| Show Metrics 展开 | Spend · Impressions · Clicks · Installs · CTR · CVR · CPC · CPM |

**Ad Group Tab**

| 列 | 说明 |
|----|------|
| 勾选框 / 开关 | 批量选择；Active / Paused |
| Ad Group Name / Ad Group ID | 名称与 ID |
| Campaign Name / Campaign ID | 所属 Campaign |
| Region | 投放区域，无则 `-` |
| Status | Active / Paused |
| Updated Time / Created Time | 时间戳 |
| Schedule | 如 Continuous |
| Bid | 如 `CPI: 1 USD` |
| Campaign Budget | Campaign 层预算 |
| Show Metrics 展开 | Ad Group Budget · Spend · Impressions · Clicks · Installs · CTR · CVR · CPC · CPM |

**分页：** 10 / page  

#### 4.3.3 列表操作

| 位置 | 操作 | 说明 |
|------|------|------|
| 工具栏 | **New** | 打开三步新建向导（`openAdsWizard`） |
| 工具栏 | Edit ▾ | 需勾选一条，否则 disabled（占位） |
| 工具栏 | Show Metrics | 勾选后展开指标列（Campaign 8 列 / Ad Group 9 列，见 §4.3.2） |
| 工具栏 | Custom Columns | 自定义列（占位） |
| 工具栏 | ↻ Refresh | 刷新列表 |
| 行级 | 开关 | 切换 Active / Paused |

---

### 4.4 Step 1 — Campaign

**公共：** 模式切换 **New** | **Select Existing**；副文案：*Keep both creation, historical data, and template entries according to the current 1:1 clone logic.*

#### 4.4.1 New

| 区块 | 字段 | 控件 | 说明 |
|------|------|------|------|
| Objective | APP Promote / Web Promote | 分段按钮 | 切换时自动生成 Campaign Name 前缀 |
| Campaign Settings | Campaign Name | 文本 | 默认 `APP Promote` / `Web Promote` + 时间戳 |
| Campaign Settings | Enable Budget | 开关 | 默认 Off |

#### 4.4.2 Select Existing

| 区块 | 字段 | 控件 | 说明 |
|------|------|------|------|
| History Data | 历史 Campaign | 下拉 | Tab「History Data」；placeholder *Please enter History Data name* |
| — | Objective / Campaign Settings | — | **不展示**（名称由历史数据带出，`applyCampaignHistory` 回填 objective） |

**底部操作：** Back to List · **Next Step**

---

### 4.5 Step 2 — Ad Group

**说明：** 无 New / Select Existing 切换；进入本步后直接填写完整新建表单。副文案：*Keep both creation, historical data, and template entries according to the current 1:1 clone logic.*

#### 4.5.1 Ad Group Settings

| 字段 | 控件 | 说明 |
|------|------|------|
| Ad Group Name | 文本 | 默认 `Ad Group` + 时间戳 |
| APP Promote / Web Promote | 下拉 | 标签随 Step 1 Objective 变化；选项来自 **My Apps** |

#### 4.5.2 Target

| 字段 | 选项 |
|------|------|
| Audience Type | Install · Retargeting |
| Billing Method | CPM · CPS · CPC · CPI · CPA |
| Bid Method | CPI · CPA · ROAS0 · ROAS7 |
| Price | 数字 + **USD** 后缀 |

#### 4.5.3 Audience

| 字段 | 控件 | 说明 |
|------|------|------|
| Country | 文本 | *Please enter Country name* |
| City | 文本 | *Please enter City name* |
| Language | 文本 | *Please enter Language name* |
| Audience | 开关 | 受众扩展开关 |
| Device | 三列 | **OS（可搜索）** · OS Version · Device Brand；支持 **+ Add Device** 多行 |
| Traffic | 下拉 | All Network · WiFi Only · Cellular Only |
| Data Packages | 搜索选择器 | 需先填 **Country** 并选推广 App；**+ Select Data Packages** 打开搜索面板，仅展示匹配 Country + Platform 的包；已选以芯片展示 |

**Device — OS 可选项：** All OS · Android · iOS · 其他/Amazon（搜索过滤）

#### 4.5.4 Allow & Block

投放某个 App 时，控制使用或不使用哪些**来源 App** 的流量数据（Ad Group 级覆盖；App 级默认见 Allow & Block 模块）。

| 字段 | 控件 | 说明 |
|------|------|------|
| Whitelist | 文本域 + Upload | 仅使用列表中的来源 App ID |
| Blacklist | 文本域 + Upload | 排除列表中的来源 App ID |
| Load from app-level settings | 链接 | 从 Allow & Block 载入当前所选推广 App 的默认配置 |
| Allow & Block | 链接 | 跳转 Allow & Block 子页 |

**输入规则：** 每行一个 App ID，或用逗号 / 空格分隔；支持上传 `.txt` / `.csv` 合并导入。

#### 4.5.5 Budget & Schedule

| 字段 | 控件 |
|------|------|
| Ad Group Total Budget | 数字 + USD |
| Ad Group Daily Budget | 数字 + USD |
| Delivery Time | Continuous · **Time Range**（显示 Start / End 日期） |
| Time Slot | All Day · **Parted**（时区 + 7×24 网格 + Selected 摘要 + Clear Selected） |

**Time Slot 网格：** 行 = Mon–Sun；列 = 0–23；点击选中；摘要格式如 `Monday04:00-05:00`

#### 4.5.6 Tracking Links

| 字段 |
|------|
| Impression URL |
| Click Through URL |
| Click Track URL |

**底部操作：** Back to List · **Next Step**

---

### 4.6 Step 3 — Creative

**公共：**

1. 模式 **New** | **Select Existing**
2. 创意类型 **Creative** | **Creative Group**（两种模式均可用）

#### 4.6.1 Select Existing

| 字段 | 控件 | 说明 |
|------|------|------|
| History Data | 下拉 | Tab「History Data」 |
| 选项来源 | — | 选 **Creative** → `creativeHistoryData`；选 **Creative Group** → `creativeGroupHistoryData` |

#### 4.6.2 New + Creative

与 Manage Creative → Creatives 新建页一致（不含「Apply to Creative Group」区块）：

| 区块 | 说明 |
|------|------|
| Creative Type | Banner · Native Image · Video · Video EndCard · Playable |
| Upload | 拖拽/点击；单文件 image/video |
| Title / Description | 文本 |
| Native CTA Text | 文本；右侧手机预览同步 CTA |
| 预览 | 右侧 For You 卡片 mock |

#### 4.6.3 New + Creative Group

与 Manage Creative → **New Creative Group** 弹窗字段一致（内嵌于向导，非弹窗）：

| 字段 | 说明 |
|------|------|
| Creative Group Name * | 必填 |
| Creative Type | 多选 checkbox |
| Creatives | Upload · Add Existing Creative · Delete Selected；素材表格 |
| Country | 默认 ALL |

**底部操作：** Back to List · **Save**

| Save 行为 | 说明 |
|-----------|------|
| Creative（New） | 写入 `assets`，Toast，关闭向导 |
| Creative Group（New） | 写入 `groups`，Toast，关闭向导 |
| Select Existing | 校验已选 History Data，Toast，关闭向导 |

---

### 4.7 向导通用交互

| 交互 | 说明 |
|------|------|
| 步骤条点击 | `goAdsWizardStep(1\|2\|3)` 任意步骤可跳转 |
| 步骤完成态 | 当前步骤之后的步骤号显示 ✓ |
| × / Back to List | `closeAdsWizard`，回到列表并重置步骤 |
| 打开向导 | `openAdsWizard` 重置 Campaign / Ad Group / Creative 表单 |

---

### 4.8 原型边界

| 已实现（原型） | 待迭代 |
|----------------|--------|
| 三步向导 UI 与 Mock 表单 | Edit 批量编辑 |
| History Data 下拉（Mock） | 真实历史/模板 API |
| Step 3 写入本地 Mock 数据 | 服务端持久化与校验 |
| 时区/排期/设备网格交互 | 复杂定向规则引擎 |
| Show Metrics 指标列切换（Mock） | Custom Columns · 真实指标 API |
| Targeting Settings 模块 + Allow & Block / Data Packages（Mock） | 黑白名单与数据包校验 · 与投放引擎联动 |

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
筛选（Name / Ad Unit）+ New Creative Group
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
| Creative Type | 多类型标签 |
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
- 列表不再展示 Combination Method / Deliverable Ad Types（Mock 数据仍保留 `method` / `adTypes` 字段供后续扩展）  

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
> **粒度：** Campaign / Ad Group（**不含素材维度**；素材下钻见 Creative Monitoring）

### 9.1 模块定位

按多维条件查询 **广告计划 / 广告组** 层级投放效果，自定义维度与指标列，支持导出。

与 Creative Monitoring 的分工：

| | Ad Monitoring | Creative Monitoring |
|--|---------------|---------------------|
| 分析粒度 | Campaign / Ad Group | Creative / Creative Group |
| 素材筛选项 | 无 | 有 |
| 素材维度 | 无 | 有 |

### 9.2 页面结构

```
查询条件（Timezone / Date / Campaign / Ad Group / Region）
    ↓
Data Dimensions（时间 + Campaign + Ad Group + Country）
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
| 7 | Delivery Region | 文本 |

> **已从本页移除（归 Creative Monitoring）：** Creative Name / ID、Creative Group Name / ID、Creative Type。

### 9.4 Data Dimensions

**默认勾选：** Day · Campaign Name · Ad Group Name  

**可选：** Hour / Month / Campaign ID / Ad Group ID / Country · Region  

> **已从本页移除：** Creative Group Name/ID、Creative Name/ID、Creative Type。

### 9.5 Data Indicators

通过 **Configure Columns** 配置。默认选中：

`Impressions, Clicks, CTR, Installs, CPI, IVR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS`

完整字段库见 [附录 A](#附录-a报表指标字段库)（两报表指标库共用）。

> 已明确 **移除**：Advanced Options、CSV Batch Block Traffic。

### 9.6 结果列表

列 = 已选 Dimensions + 已选 Indicators；列头可排序；分页 50 / page；空态 No data。

**Mock 示例维度列：** Day, Campaign Name, Ad Group Name  

**Mock 示例指标列：** Impressions, Clicks, CTR, Installs, CPI, IVR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS  

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
> **粒度：** Creative / Creative Group（素材监控专用）

### 10.1 模块定位

按 **素材 / 素材组** 维度分析投放效果；保留完整素材筛选项与维度，与 Ad Monitoring 形成互补。

### 10.2 页面结构

```
查询条件（Timezone / Date / Campaign / Ad Group / Creative / Creative Group / Type / Region）
    ↓
Data Dimensions（时间 + Campaign + Ad Group + Creative + Creative Group + Country）
    ↓
Data Indicators 摘要 + Configure Columns
    ↓
Search Data / Export Data
    ↓
结果表（可排序）+ 分页
```

### 10.3 筛选（查询条件，共 12 项）

| # | 字段 | 控件 |
|---|------|------|
| 1 | Timezone | 下拉 |
| 2 | Date Range | 文本 |
| 3–4 | Campaign Name / ID | 文本 |
| 5–6 | Ad Group Name / ID | 文本 |
| 7–8 | Creative Name / ID | 文本 |
| 9–10 | Creative Group Name / ID | 文本 |
| 11 | Creative Type | 下拉（Banner / Native Image / Video / Video EndCard / Playable） |
| 12 | Delivery Region | 文本 |

### 10.4 Data Dimensions

**默认勾选：** Day · Ad Group Name · Creative Name · Creative ID  

**完整可选：** Hour / Month · Campaign Name/ID · Ad Group Name/ID · Creative Group Name/ID · Creative Name/ID · Creative Type · Country / Region  

### 10.5 Data Indicators

与 §9 共用字段库与默认列；默认选中：

`Impressions, Clicks, CTR, Installs, CPI, IVR, Spend, Revenue, ROI, CPM, D0 total rev, D0 total ROAS`

### 10.6 结果列表

**Mock 示例维度列：** Day, Ad Group Name, Creative Name, Creative ID  

**Mock 示例指标列：** 同 §9.6  

### 10.7 操作

Search Data / Export Data / Configure Columns — 与 §9.7 相同。

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
| Platform | 空 / iOS / Android / Mix |
| Status | 空 / Active / Paused |

### 11.4 列表

| 列 | 说明 |
|----|------|
| 勾选 + 开关 | 批量选择；Active / Paused |
| App Name | 图标 + 名称 |
| Status | Active / Paused 徽章 |
| Actions | Edit + **Allow & Block** + **Events** |
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
| Allow & Block | 跳转 Allow & Block 并打开该 App 的配置弹窗 |
| Events | 跳转 Events 并打开该 App 的事件配置弹窗 |

**弹窗字段（自上而下）：**

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| Icon | 否 | 方形上传区 | 置于表单最上方；虚线方框内 **+**；点击上传图片；有图时预览；未上传时保存默认 emoji（iOS 🍎 / Android 🤖） |
| App Name | 是 | 文本 | |
| Platform | 是 | 下拉 | iOS / Android |
| App ID (Bundle ID) | 是 | 文本 | |
| Preview Link | 否 | 文本 | App 预览链接，placeholder `https://` |
| Daily Available Budget | 条件 | 数字 + **$** 前缀 | 勾选 **No budget limit** 时禁用；未勾选时需填写 &gt; 0 |
| No budget limit | 否 | 勾选框 | 默认勾选；不设每日预算上限 |
| Timezone | 否 | 下拉 | UTC+0 / UTC+8 / UTC-5 |

**Daily Available Budget 说明（ⓘ）：**

1. 当日预算消耗完毕后，该 App 下广告及关联 Ad Unit 自动关停  
2. 每日预算按 **GMT-8** 时区计算与生效  

**列表 Icon 展示：** 图片 URL / data URL 显示缩略图；否则显示 emoji 或名称首字母。

**新建默认：** Active=true；Spend/CVR/CPI=`--`；Linked Ad Units=0；**No budget limit=true**

---

## 12. Targeting Settings

> **入口：** 侧栏 Targeting Settings（可展开子菜单）  
> **适用对象：** 投放运营  

Targeting Settings 为一级导航分组，包含两个子模块：

| 子菜单 | page 标识 | 用途 |
|--------|-----------|------|
| **Allow & Block** | `targeting-list` | 按 My Apps 中的推广 App 配置来源 App 黑白名单 |
| **Data Packages** | `targeting-data-packages` | 上传受众设备 ID 数据包，供 Ads Audience 选用 |
| **Events** | `targeting-events` | 按 My Apps 中的推广 App 配置转化事件 |

> **命名说明：** 黑白名单子菜单为 **Allow & Block**；**Events** 用于 App 级事件名称与说明配置；列表 **App ID** 列与 My Apps 命名一致。

---

### 12.1 Allow & Block

> **面包屑：** Home > Targeting Settings > Allow & Block  
> **入口：** 子菜单 Allow & Block；My Apps 行内 **Allow & Block**；Ads Step 2 链接  

#### 12.1.1 模块定位

从 **My Apps** 选择一个推广 App，为其配置来源 App 的 **Whitelist**（允许）与 **Blacklist**（禁止）。列表展示已配置的 App，而非 My Apps 全量列表。

#### 12.1.2 页面结构

```
页头 + New Config
    ↓
说明文案
    ↓
筛选（App Name / 列表类型）
    ↓
配置列表 + 分页
    ↓
New / Edit 弹窗（App 选择 + Whitelist / Blacklist）
```

#### 12.1.3 筛选

| 字段 | 选项 |
|------|------|
| App Name | 模糊匹配 |
| 列表类型 | All · Has whitelist · Has blacklist · Empty |

#### 12.1.4 列表

| 列 | 说明 |
|----|------|
| App Name | 图标 + 名称 |
| App ID | 与 My Apps 一致 |
| Platform | iOS / Android / Mix |
| Whitelist | ID 数量徽章 |
| Blacklist | ID 数量徽章 |
| Updated Time | 最后更新时间 |
| Actions | Edit · Delete |

#### 12.1.5 弹窗字段

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| App | 是 | 下拉 | 来自 My Apps；每个 App 仅一条配置；编辑时不可改 App |
| Whitelist | 否 | 文本域 + Upload | 来源 App ID，支持 `.txt` / `.csv` 合并导入 |
| Blacklist | 否 | 文本域 + Upload | 同上 |

#### 12.1.6 操作与联动

| 操作 / 入口 | 说明 |
|-------------|------|
| + New Config | 从尚未配置的 My Apps 中选择 App 并新建 |
| Edit / Delete / Save | 标准 CRUD，写入 `targetingSettingsData` Mock |
| My Apps → Allow & Block | 跳转并打开对应 App 弹窗 |
| Ads Step 2 → Load from app-level settings | 将 App 级配置填入 Ad Group 表单 |

---

### 12.2 Data Packages

> **面包屑：** Home > Targeting Settings > Data Packages  
> **入口：** 子菜单 Data Packages；Ads Step 2 Audience → Manage Data Packages  

#### 12.2.1 模块定位

上传受众**设备 ID** 文件（IDFA、GAID 等），形成可复用的定向数据包，在 Ads Step 2 **Audience** 中通过搜索选择器绑定。

#### 12.2.2 页面结构

```
页头 + New Package
    ↓
说明文案
    ↓
筛选（Package Name / Package ID / Platform）+ Search
    ↓
数据包列表 + 分页
    ↓
New / Edit 弹窗
```

#### 12.2.3 筛选

| 字段 | 选项 |
|------|------|
| Package Name | 模糊匹配 |
| Package ID | 模糊匹配 |
| Platform | 空 / iOS / Android / Mix |

#### 12.2.4 列表

| 列 | 说明 |
|----|------|
| ID | 如 `dp-10001` |
| Package Name | 数据包名称 |
| Device Type | 所含设备类型，如 IDFA · GAID |
| Platform | iOS / Android / Mix |
| Country | Global · United States · China · United Kingdom · Japan | 与 Ads Audience **Country** 字段命名统一 |
| Files | 已上传文件名芯片 |
| Created Time | 创建时间 |
| Actions | Edit · Delete |

**空态：** No data packages found  

#### 12.2.5 弹窗字段

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| Platform | 是 | 下拉 | **iOS** · **Android** · **Mix**；编辑时不可改；切换后 Device Type 选项联动 |
| Country | 是 | 下拉 | Global · US · CN · GB · JP；与 Ads Country 匹配规则见 §12.2.7 |
| Package Name | 是 | 文本 | |
| Upload Files | 是 | 表格行 | 每行：Device Type 下拉 + 文件上传 + Delete；**+ Add device type** 可增行 |

**Device Type 随 Platform 联动：**

| Platform | 可选 Device Type |
|----------|------------------|
| iOS | IDFA · IDFA_MD5 |
| Android | IMEI · GAID · OAID · IMEI_MD5 · GAID_MD5 · OAID_MD5 |
| Mix | IMEI · IDFA · GAID · OAID · IMEI_MD5 · IDFA_MD5 · GAID_MD5 · OAID_MD5 |

弹窗 Platform 下方展示当前平台可用类型；**+ Add device type** 在达到上限后禁用。

**文件约束：** `.txt` · `.csv` · `.gz`，单文件 ≤ 500MB（原型 Mock 解析行数）  

#### 12.2.6 操作

| 操作 | 说明 |
|------|------|
| + New Package | 打开新建弹窗 |
| Edit / Delete / Save | 写入 `dataPackages` Mock |
| Search | 刷新列表（Toast） |

#### 12.2.7 与 Ads 联动

**选择流程（Step 2 Audience）：**

1. 填写 **Country** 并选择推广 **App**（确定 Platform）
2. 点击 **+ Select Data Packages** 打开搜索面板
3. 面板内仅列出同时匹配 Country + Platform 的包（`Global` 国家包匹配任意 Country）
4. 支持按名称 / ID 搜索；选中后以芯片展示，可单独移除

**匹配规则：**

| 维度 | 规则 |
|------|------|
| Platform | 与 App OS 一致，或包 Platform 为 **Mix** |
| Country | 包 Country 为 **Global**，或与 Audience Country 文本模糊匹配（如 `US` / `United States`） |

- 链接 **Manage Data Packages** 跳转本子页  
- Country 或 App 变更时，自动移除不再匹配的已选包  

---

### 12.3 Events

> **面包屑：** Home > Targeting Settings > Events  
> **入口：** 子菜单 Events；My Apps 行内 **Events**  

#### 12.3.1 模块定位

从 **My Apps** 选择一个推广 App，配置一个或多个**转化事件**（Event Name + Event Description），供投放追踪与优化使用。

#### 12.3.2 页面结构

```
页头 + New Events
    ↓
说明文案
    ↓
筛选（App Name / Event Name）
    ↓
事件列表（按事件行展开）+ 分页
    ↓
New / Edit 弹窗
```

#### 12.3.3 列表

| 列 | 说明 |
|----|------|
| App | 图标 + 名称 |
| App ID | 与 My Apps 一致 |
| Event | 事件名称 |
| Event Description | 事件说明 |
| Updated Time | 最后更新时间 |
| Actions | Edit（打开该 App 全部事件）· Delete（删除单条事件） |

**空态：** No events found  

#### 12.3.4 弹窗字段

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| App | 是 | 下拉 | 来自 My Apps；每个 App 仅一条事件配置；编辑时不可改 App |
| Event Settings | 是 | 表格行 | Event Name · Event Description · Delete |
| + Custom Event | — | 按钮 | 新增空行 |

**校验：** 至少一条 Event Name 非空；Save 写入 `appEventsData` Mock。

#### 12.3.5 操作与联动

| 操作 / 入口 | 说明 |
|-------------|------|
| + New Events | 为尚未配置的 App 新建 |
| My Apps → Events | 跳转并打开对应 App 弹窗 |
| Delete（列表行） | 删除单条事件；若 App 下无事件则移除整个配置 |

---

### 12.4 状态字段

| 字段 | 说明 |
|------|------|
| `appEventsData` | Events Mock 数据（按 App 聚合） |
| `eventModal` | Events 弹窗状态 |
| `dataPackages` | Data Packages Mock 数据 |
| `dataPackageModal` | Data Packages 弹窗状态 |
| `adsAdGroupForm.dataPackageIds` | Step 2 已选数据包 ID 数组 |

---

## 13. Account Settings · Account Info

> **入口：** 用户下拉 → Account settings → Account info  
> **页面标识：** `account-settings` + `settingsTab=account-info`  

### 13.1 模块定位

维护账户主体信息（账户名、公司、税号、推广网站）。

### 13.2 页面结构

```
独立全屏：返回 + 左侧 Tab（Account info / Members / Payment & billing）
    ↓
头像 / Account Name / Account ID（只读）
    ↓
可编辑表单 + Save
```

### 13.3 表单字段

| 字段 | 说明 |
|------|------|
| Account name | 同步顶部用户名显示 |
| Company name | |
| Tax ID | |
| Website to promote | 前缀 `https://` + 域名 |

### 13.4 操作

| 操作 | 说明 |
|------|------|
| Back | 返回进入设置前的页面 |
| Save | 保存，Toast |
| 左侧 Tab | 切换三个设置子页 |

---

## 14. Account Settings · Members

> **入口：** Account settings → Members  
> **Tab：** `members`  

### 14.1 模块定位

管理子账号：查看 Owner、邀请用户并配置权限与有效期。

### 14.2 页面结构

```
筛选（email / Access level）+ Invite new user
    ↓
Account Owner 卡片
    ↓
All users 表 + 分页
    ↓
Invite 弹窗
```

### 14.3 筛选

| 字段 | 说明 |
|------|------|
| Search users by email | 模糊匹配邮箱 |
| Access level | 空 / Owner / Admin / Read-only |

### 14.4 展示

**Owner 卡片：** 头像、Account owner 标签、Email  

**用户表列：** User email · Last signed-in (UTC) · Access level · Expiration (UTC) · App access  

**分页：** 10 / page，含 Go to、Rows per page  

### 14.5 Invite 弹窗

| 字段 | 必填 | 控件 | 说明 |
|------|------|------|------|
| Email addresses | 是 | 文本 | 逗号分隔多邮箱 |
| Password | 是 | 密码 | 子账号初始密码 |

底部：Cancel / Invite  

> 弹窗内不再配置 Access level / Access expiration / App access；邀请成功后成员列表仍展示 Access level 等列（新建成员原型默认 Read-only · Never · All apps）。完整权限模型待后端对接。

---

## 15. Account Settings · Payment & Billing

> **入口：** Account settings → Payment & billing  
> **Tab：** `billing`  

### 15.1 模块定位

查看余额、支付方式、账单流水与账单地址，支撑充值与自动扣款。

### 15.2 页面结构

```
Balance 卡片 | Payment Method 卡片
    ↓
All billing activities（筛选 + 表）
    ↓
Billing Address 卡片
    ↓
Add Card / Add Billing Information 弹窗
```

### 15.3 筛选（账单流水）

| 字段 | 说明 |
|------|------|
| Search by ID | 按流水 ID |
| 时间范围 | All time（可扩展） |
| Category / Status | Chip 下拉 |

### 15.4 展示

**Balance：** Balance · Added funds · Ad credit · Held for spend  

**Payment Method：** 无卡空态 / 有卡展示 VISA 末四位 + 过期 + Auto reload  

**流水表列：** ID · Time (UTC) · Category · Description · Amount · Payment method · Status；分页 50 / page  

**Billing Address：** 已填地址或 `—`  

### 15.5 操作

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

## 16. 附录

### 附录 A：报表指标字段库

广告报表与素材报表 **Configure Columns** 共用。日维度序列：**D0, D1, D3, D7, D14, D28, D30**。

#### A.1 Basic

Impressions, Clicks, CTR, Installs, CPI, **eCPI**, **IVR**, Spend, Revenue, ROI, CPM, **eCPM**, **eCPC**

> **IVR**（Install Conversion Rate / 安装转化率）：原 IR 字段，两报表默认指标列均使用 IVR 命名。

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
| Targeting | Allow & Block | `targeting-list` |
| Targeting | Data Packages | `targeting-data-packages` |
| Targeting | Events | `targeting-events` |
| Settings | Account Settings | `account-settings` |

---

### 附录 C：跨模块指标对齐

| 指标 | Overview | Risk Control | Reports |
|------|----------|--------------|---------|
| Spend / Revenue / ROI | ✓ | —（间接看预算） | ✓ Basic |
| eCPI / eCPM / eCPC | ✓ | eCPI Spike / Campaign Actual | ✓ Basic |
| IVR | — | — | ✓ Basic（默认列） |
| D0/D7/D30 AU | ✓ | — | ✓ Active users |
| CPI / CPM | —（看板用 e*） | Goal 可用 CPI | ✓ 并存 |
| Goal / Pacing / Review | — | ✓ 主战场 | — |

---

### 附录 F：广告报表 vs 素材报表对照

| 对比项 | Ad Monitoring | Creative Monitoring |
|--------|---------------|---------------------|
| page 标识 | `reports-ad-monitor` | `reports-creative-monitor` |
| 分析粒度 | Campaign / Ad Group | Creative / Creative Group |
| 查询条件数 | 7 | 12 |
| 含素材筛选项 | 否 | 是（Creative / Group / Type） |
| 含素材维度 | 否 | 是 |
| Dimensions 默认 | Day · Campaign Name · Ad Group Name | Day · Ad Group Name · Creative Name · Creative ID |
| 指标库 / 默认列 | 共用 | 共用 |
| 默认指标含 IVR | 是 | 是 |
| 典型下钻路径 | 看 Campaign 花费与目标 | 看单条 Creative 效果 |

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
| [REQUIREMENTS.md](./REQUIREMENTS.md) | 全模块产品需求（本文，v1.11） |
| [RISK_CONTROL.md](./RISK_CONTROL.md) | Risk Control 专项说明（字段字典、告警跳转、运营读盘顺序） |

---

*文档结束 · v1.11*
