# 可撤销身份机制（references/identity-revocation.md）

> v6 原则 8（健康度挂钩）的延伸。借鉴 Wikipedia desysop + Stripe pod rotation + ICS Transfer of Command。

## 为什么要"可撤销"

调研发现：
- Linux 2018 休假事件 → 单点无接班人
- XZ Utils 2024 后门 → 单点疲惫无监督
- Valve 隐性等级 → 无 accountability

**解药**：身份必须有保质期 + 续期评审 + 可撤销机制。

## 三层机制

### 第 1 层：身份保质期（默认开启）

- 每个 IDENTITY 卡有"入场时间 + 退场时间"
- 默认保质期：每 4 周（与重议周期对齐）
- 到期触发续期评审

### 第 2 层：续期评审（默认开启）

- 每 4 周 / 每 milestone 触发
- main 召集 + 相关 agent 投票
- 三选一：续期 / 撤销 / 重定义

### 第 3 层：可撤销（默认开启）

任何身份都可被撤销：
- main 决定撤销（理由写 PR）
- 健康度告警触发自动撤销（如 main 负载 > 阈值）
- 投票决定撤销（co-main / 委员会）
- 用户明示撤销

## 撤销流程

```
1. 在登记表把状态从 [招] 改 [撤]
2. IDENTITY 卡归档到 roles/archive/
3. 写撤销说明（roles/decisions/revoke-<date>-<name>.md）：
   - 撤销者
   - 撤销理由
   - 是否需要接班人
4. 若需要接班人，走招聘流程招新
5. 走 GitHub PR + reviewer approve
```

## 借鉴案例

### Wikipedia admin（desysop）

- 来源：Wikipedia:Requests for adminship
- 机制：admin 身份可被 RfA（社区投票）撤销（desysop）
- 启示：身份可逆 = 权力可问责

### ICS（Incident Command System）

- 来源：FEMA ICS 教材
- 机制：角色寿命 = 事件寿命；首到单位建立指挥权；可随升级移交
- 启示：身份有"过期时间"，不是永久

### Stripe pod rotation

- 来源：Stripe Sessions talks
- 机制：2-6 个月跨 pod 轮换
- 启示：强制轮换防止单点疲惫 + 知识传播

### Toyota Production System / GE Job Rotation

- 来源：Toyota / GE HR 实践
- 机制：18-24 个月岗位轮换
- 启示：短期生产力下降 5-15%，长期 resilience 提升

## 与 v6 原则的关系

- 原则 5（规模自适应）：撤销后身份数减少，可能触发重新评估
- 原则 7（可信度重议）：重议可包括"撤销某验证岗"
- 原则 8（健康度挂钩）：告警触发自动撤销

## 不要做的事

- 不要让身份"永久有效"——必须有保质期
- 不要让 main 单方面撤销而不走 PR
- 不要让撤销流程跳过健康度审查
- 不要让撤销后无人接班——必须先有接班人再撤销（或同时撤销 + 招新）