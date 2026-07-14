# Risk Control Dashboard 说明文档

> **版本：** v1.1  
> **更新日期：** 2026-07-14  
> **入口：** 左侧导航 Dashboard → Risk Control  
> **页面标识：** `dashboard-risk`  
> **对标：** AppLovin（AXON Ads Manager）广告主日常盯盘逻辑  
> **适用对象：** 产品、运营、投放、测试  
> **关联文档：** [全模块需求文档](./REQUIREMENTS.md)

---

## 1. 模块定位

Risk Control 是账户级**风险与健康监控看板**，回答四个问题：

1. 账户钱够不够、还能花几天？  
2. Campaign 预算是否超花 / 花不完？  
3. 投放目标（CPI / CPP / ROAS）是否达标？  
4. 素材审核是否卡住 / 被拒？

与 **Overview** 的分工：

| | Overview | Risk Control |
|--|----------|--------------|
| 视角 | 效果看数（Spend / Revenue / eCPI / AU…） | 风险与干预（预算、目标、审核、告警） |
| 典型动作 | 分析趋势、下钻报表 | 发现异常 → 跳转 Ads / Creative / Billing |

设计参考 AppLovin 广告主关注点：**日预算 pacing、Goal 达标、素材审核、eCPI/ROAS 突变**；弱化交易所视角的 Win Rate / Bid Floor。

---

## 2. 页面结构

自上而下：

```
筛选条（App / Date / Timezone）+ Refresh
    ↓
Account Health（评分 + 摘要 + Tags）
    ↓
KPI × 4
    ↓
Budget Progress  |  Delivery Health
    ↓
Campaign Budget Status（全宽表）
    ↓
Creative Review Queue  |  Risk Alerts
```

---

## 3. 筛选与刷新

### 3.1 筛选

| 字段 | 说明 | 影响范围 |
|------|------|----------|
| App | All Apps / 各 App | **Campaign 表**按 App 过滤 |
| Date Range | 统计时间范围（原型为文本） | 预留；正式版驱动聚合口径 |
| Timezone | UTC+0 / UTC+8 / UTC-5 | 预留；正式版对齐「今日」边界 |

### 3.2 操作

| 操作 | 说明 |
|------|------|
| ↻ Refresh | 刷新看板数据；更新 `Updated HH:MM:SS` 时间戳 |

---

## 4. Account Health（账户健康）

### 4.1 说明

账户整体健康快照，用于「一眼判断要不要往下看」。

| 元素 | 说明 |
|------|------|
| Health Score | 0–100 评分（原型 Mock；正式版建议由余额、pacing、goal、素材审核等加权） |
| Health Label | 如 Good / Fair / Poor |
| Summary | 一句话总结当前主要风险 |
| Tags | 状态标签，可点击联动 |

### 4.2 Tags 等级

| 等级 class | 含义 |
|------------|------|
| good | 正常 |
| warn | 需关注 |
| bad | 严重/立即处理 |

### 4.3 Tags 点击行为（原型）

| Tag 示例 | 点击后 |
|----------|--------|
| Payment OK | 跳转 Account Settings → Payment & billing |
| Goal Drift | Toast 提示查看下方 Campaign 表 |
| Budget Pacing Warning | 滚动到 Risk Alerts 区域 |
| Account Active | Toast 展示标签名 |

---

## 5. KPI 卡片（4 个）

偏 AppLovin 广告主语义，强调「钱、目标、审核、跑道」。

| KPI | 含义 | 如何解读 |
|-----|------|----------|
| **Available Balance** | 可用余额 | 搭配 footer 看是否开启 auto-recharge |
| **Campaigns Off Goal** | 未达目标的 Campaign 数 | 有数量即应打开下方 Campaign 表查 Goal Status |
| **Creatives Pending Review** | 待审素材数 | 对照 Creative Review Queue |
| **Burn Days Left** | 按当前日消耗估算的可消耗天数 | 接近 auto-recharge 阈值时应补款/调预算 |

> Burn Days ≈ Available Balance ÷ 当日/近几日平均 Spend（原型为 Mock 展示值）。

---

## 6. Budget Progress（预算进度）

### 6.1 说明

监控账户/当日预算是否按计划消耗，避免**超预算**或**花不完**。

| 行 | 说明 | 进度条颜色规则（原型） |
|----|------|------------------------|
| Monthly Budget | 本月已花 / 月上限 / 使用率 | >90% 偏橙，否则蓝 |
| Today's Spend | 当日已花 / 日上限 / 使用率 | >100% 红；>85% 橙；否则绿 |
| 7-Day Spend Pacing | 近 7 日柱状 pacing，含目标线 | 柱高 = 实际节奏；黄线 = target |

### 6.2 运营解读

- **Today > 100%**：超日预算，可能限流或超花，优先看 Campaign 表 Over Budget 行。  
- **Today 明显偏低**：Under Pace，检查出价、定向、素材是否可投。  
- **7 日柱持续低于目标线**：系统性 under-delivery，而非单日波动。

---

## 7. Delivery Health（投放健康）

广告主目标与投放质量摘要（非竞价侧 Win Rate）。

| 指标 | 含义 | 状态标签 |
|------|------|----------|
| **Goal Attainment Rate** | 达标 Campaign 占 Live Campaign 比例 | Healthy / Watch / Action |
| **eCPI Spike** | eCPI 相对前 7 日均值的涨幅 | Watch 表示成本异常抬升 |
| **Under-delivery** | 日预算消耗低于目标阈值（如 80%）的 Campaign 数 | Action 需干预 |
| **Creative Rejected** | 拒审素材 / 提交总量 | Watch |
| **Pending Review** | 待审素材数 | Action（可能影响放量） |

状态等级：`good`（Healthy）/ `warn`（Watch）/ `bad` 或需处理（Action）。

---

## 8. Campaign Budget Status（Campaign 预算与目标）

### 8.1 说明

核心明细表。可同时看出两类风险：

1. **预算风险**：超预算 / Near Cap / Under Pace  
2. **目标风险**：Goal Status = Off Track（eCPI 超目标、ROAS 不达标等）

筛选 App ≠ All Apps 时，仅展示该 App 下的 Campaign。

### 8.2 列表字段

| 列 | 说明 |
|----|------|
| Campaign | Campaign 名称 |
| Status | Live / Paused / Out of Budget |
| Daily Budget | 日预算 |
| Spent Today | 当日已花费 |
| Progress | 消耗进度条 + 百分比 |
| Pacing | On Pace / Near Cap / Over Budget / Under Pace |
| Goal | 目标值 + 目标类型（CPI / CPP / ROAS） |
| Actual | 实际表现（如 `$2.35 eCPI`、`86% D0 ROAS`） |
| Goal Status | On Track / Off Track |

### 8.3 Status / Pacing / Goal Status 字典

**Campaign Status**

| 值 | 含义 |
|----|------|
| Live | 投放中 |
| Paused | 已暂停 |
| Out of Budget | 已触及预算上限或超花受限 |

**Pacing**

| 值 | 含义 |
|----|------|
| On Pace | 消耗节奏正常 |
| Near Cap | 接近日预算上限 |
| Over Budget | 已超过日预算 |
| Under Pace | 消耗明显低于预期 |

**Goal Status**

| 值 | 含义 |
|----|------|
| On Track | 实际指标达到或优于目标 |
| Off Track | 实际差于目标（如 eCPI 偏高、ROAS 偏低） |

**Goal Type 与 Actual 对齐**

| Goal Type | Goal 示例 | Actual 示例 |
|-----------|-----------|-------------|
| CPI | $2.50 | $2.35 eCPI |
| CPP | $8.00 | $7.40 CPP |
| ROAS | 120% D0 | 86% D0 ROAS |

指标命名与 Overview / Reports 一致（eCPI、ROAS 等）。

---

## 9. Creative Review Queue（素材审核队列）

### 9.1 说明

跟踪素材政策审核状态，避免「预算有、素材不可投」导致 under-delivery。

### 9.2 展示

| 区块 | 内容 |
|------|------|
| 统计 | Pending / Rejected / Approved 数量 |
| 最近列表 | 素材名、类型、状态、相对时间 |
| 操作 | **Manage Creative** → 跳转 Manage Creative → Creatives |

### 9.3 状态

| 状态 | 含义 |
|------|------|
| Pending | 待审，尚不可投或受限 |
| Rejected | 拒审，需改创意后重提 |
| Approved | 已通过 |

---

## 10. Risk Alerts（风险告警）

### 10.1 说明

可操作告警列表：过滤级别 → View 跳转处理页 → Mute 暂时静音。

### 10.2 级别过滤

| 过滤器 | 对应 level | 含义 |
|--------|------------|------|
| All | 全部未静音 | 默认 |
| Critical | `bad` | 需立即处理（如超日预算） |
| Warning | `warn` | 需关注（off goal、拒审、under-delivery、eCPI spike） |
| Info | `info` | 提示（如余额阈值临近） |

### 10.3 操作

| 操作 | 说明 |
|------|------|
| View | 按告警 `target` 跳转对应模块 |
| Mute 24h | 原型将告警标为 muted，列表中隐藏（正式版应按用户+时长持久化） |

### 10.4 View 跳转映射

| target | 跳转页 |
|--------|--------|
| `ads` | Ads |
| `manage-creative` | Manage Creative → Creatives |
| `reports-ad-monitor` | Ad Monitoring Report |
| `account-settings` | Account Settings → Payment & billing |

### 10.5 典型告警类型（Mock）

| 级别 | 标题 | 业务含义 |
|------|------|----------|
| Critical | Daily budget exceeded | 日预算超花 |
| Warning | Campaign off goal (ROAS) | 目标未达标 |
| Warning | Creative rejected | 素材拒审/待审 |
| Warning | Under-delivery detected | 消耗不足 |
| Warning | eCPI spike | 成本环比飙升 |
| Info | Balance threshold notice | 余额临近自动充值阈值 |

---

## 11. 状态色规范（全页通用）

| class | 语义 | 典型场景 |
|-------|------|----------|
| good | 正常 | On Pace、On Track、Live、Approved |
| warn | 预警 | Near Cap、Under Pace、Pending、Watch |
| bad | 严重 | Over Budget、Off Track、Out of Budget、Rejected、Critical |

---

## 12. 推荐阅读顺序（运营）

1. 看 **Health Score + Tags** — 有没有 warn/bad  
2. 看 **KPI** — Off Goal / Pending / Burn Days 是否异常  
3. 看 **Today's Spend** — 超花还是花不完  
4. 打开 **Campaign 表** — 定位是哪条 Campaign、是预算问题还是 Goal 问题  
5. 对照 **Creative Review** — 是否素材不可投导致投放差  
6. 在 **Alerts** 点 View 去对应模块处理  

---

## 13. 与报表字段对齐说明

Risk Control 展示的效果类字段与 Overview / Reports 保持一致：

- **eCPI**（有效安装成本）  
- **ROAS / D0 ROAS**  
- **CPP**（若 Goal 为 CPP）  

报表侧默认指标含 **IVR**（安装转化率），Risk 页不单独展示 IVR。

不在此页堆完整报表指标；下钻请用 Ad Monitoring Report（Campaign/Ad Group）或 Creative Monitoring Report（素材）。

---

## 14. 当前原型边界

| 项 | 说明 |
|----|------|
| 数据 | 全部 Mock，Refresh 仅更新时间戳 |
| App 筛选 | 目前仅过滤 Campaign 表 |
| Mute | 仅前端会话内隐藏，刷新页面会恢复 |
| Health Score | 固定 Mock，未接真实加权公式 |
| 自动 Pause Campaign | 未实现（需权限与审计，计划暂缓） |

---

## 15. 后续可迭代（未做）

| 优先级 | 项 |
|--------|----|
| P1 | Goal Pacing Matrix（按 CPI/ROAS 类型看 On Track 分布） |
| P1 | Burn Rate / Runway 独立卡片（充值 ETA） |
| P2 | Geo / Ad Group 级风险下钻 |
| P2 | Alert Rules 阈值配置（Spend>100%、eCPI>+20% 等） |
| — | 深度 IVT / 竞价热力图 / 自动 Pause |

---

*文档结束 · v1.1*
