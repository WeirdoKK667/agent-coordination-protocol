---
name: agent-coordination-protocol
description: 多 agent / 多终端协作场景的协调协议——中大型项目团队组建、给 subagent 发身份牌（身份卡 / 角色卡 / id-card）、划定职责边界、维护 read 最小契约、派发任务单（dispatch）与交接单（handoff）、独立验证岗（verifier）、session 接续点、团队重组时使用。默认身份只有 main-designer，其他岗位按项目需要从 references/role-catalog.md 候选清单里"招聘"，不预设岗位清单，跨领域通用。覆盖：派发 / 交接闭环、反向纠错、commit 锚点时序、串行接单、中断恢复、测试员模式 A/B/C、占位登记表、认领协议、测试可信度条款。当用户提到协调多个 agent、拆分职责、派活给另一个终端、多 agent 协作 workflow 时应当加载。
---

# Agent Coordination Protocol v0.2.1

> 输入项目概况，派生身份卡体系 + 派发 / 交接闭环。
> **原则 1-4 已实战验证**（20 单闭环，其中 19 单走完 handoff）；**原则 5-8 未验证**，小项目默认关闭。

---

## 0. 八条原则（1-4 实战验证，5-8 未验证）

### 原则 1-4：默认全开（20 单闭环验证过）

1. **身份是单位、模块降级为职责内容**（§一）—— id-card-*.md 是身份卡不是模块卡
2. **占位岗不养空卡**（§一）—— 只产出占位登记表，不预生成具体占位身份卡
3. **职责隔离原则（按需触发）**（§一）—— 招验证类岗必须独立、可信度写死、不可由实现类兼任
4. **main-designer 是默认身份，不是招出来的**（v5）—— id-card-main-designer.md 由本 skill 预填，项目启动即存在

### 原则 5-8：未经实战验证，小项目默认关闭

> ⚠ 截至 2026-09-07，这 4 条**没有任何实战数据支撑**。唯一试用项目（1 人 + 2 终端 + 3 身份卡）按原则 5 自己的判定逻辑直接落进小项目旁路模式，从未触发过 5-8。
> **单人 / 双终端项目：默认关闭这 4 条。** 需要时由用户显式开启。

5. **规模自适应**（v6）—— 默认 ≤ 6 卡硬上限；模块数 ≤ 3 + 贡献者数 ≤ 2 自动进小项目旁路模式
   - **突破通道**：main-designer 招聘决策可主动调整上限（理由写 PR）；用户调用时特别注明特定需求不硬
   - **留痕**：所有突破 ≤ 6 的决策必须写明理由，不可静默
6. **协议即代码**（v6）—— 登记表 / 认领协议 / 可信度条款与项目代码同仓，走 GitHub PR + review
   - ⚠ **已知冲突**：若项目 `.gitignore` 排除了协议状态文件所在目录（如整个 `.claude/`），本原则**没有可执行载体**。开启前先确认状态文件能 `git add`，否则 commit 锚点全部失效。实战中踩过：断电中断导致派发单丢失。
7. **可信度写死 + 强制重议**（v6）—— §四 4 条 + 每 4 周/里程碑重议评审（Stripe RFS），可修改不可删
8. **健康度挂钩**（v6）—— 自动算 CHAOSS 指标（bus factor / main-designer 负载 / 贡献者变更率 / issue 解决时间），触发阈值自动告警——告警必须响应

**可选机制**（默认关闭，同样未验证）：⑨ 真冗余验证（N 身份并行投票，警示多 LLM 17× 错误率）；⑩ 元层反思（周期性问"LLM 是否真有自治"）。

读 `references/eight-pinning-principles.md` 看速查表。

### 协议核心价值（实战数据）

**反向纠错**是本协议被验证过的最大价值：implementer 接单后核对现状，反向纠正 main-designer 的派发瑕疵。

| 指标 | 实测值 |
|---|---|
| 闭环单数 | 20 单（19 单走完 handoff） |
| 发生反向纠错的单 | 7 单（**37%**） |
| 具体纠错处数 | 16 处 |
| 触发 main-designer 回头改模板 / 改派发单 | 4 次 |

典型纠错内容：派发单声称"缺 §二/§三/…"实际已存在、引用已归档的文件、字段值写错（坐标当 id）、给出的代码位置对应到别的函数、基线数字过期。

**结论**：派发方**不做实地核验就派单**是最高频失败模式（14+ 处）。§开工前核对清单 迭代 7 轮仍未完全防住——所以这条清单不是可选项。

---

## 1. 主工作流：招聘 + 派发（main 视角）

```
0. 加载本 skill → 命中 trigger words → 确认场景
1. 规模检测：读 references/scaling-modes.md
   - 输入：模块数 / 贡献者数 / 代码量 / 现有协议复杂度
   - 输出：规模档位（小作坊 / 中型 / 大型 / 超大）
2. 默认身份 main：先落 id-card-main-designer.md（用 templates/identity-main.md 预填）
3. 规模档位决定配置：
   - 小作坊（≤ 3 模块 + ≤ 2 贡献者）→ 小项目旁路模式
     * 只发 id-card-main-designer.md
     * 占位登记表 / 任务清单 / 协议走简化模板
     * 跳过可信度写死 + 健康度挂钩（main 自己验）
   - 中型 / 大型 → 走完整流程（步骤 4-）
   - 超大（> 15 模块 或 > 8 贡献者）→ 强制分权警告
4. 招聘流程：
   4.1 读 references/eight-pinning-principles.md
   4.2 读 references/input-collection-checklist.md，按 checklist 采集项目输入
   4.3 读 references/role-catalog.md，从抽象岗位类别选岗（或自定义）
   4.4 数量硬约束：累计（含 main）默认 ≤ 6 硬上限
        - main 可主动调整上限（理由写 PR）；用户特别注明不硬
   4.5 对每个招的岗：
        - 用 templates/identity-card.md 渲染 id-card-<昵称>.md
        - 若涉及验证/测试，把 templates/test-credibility.md 原样嵌入
        - 顶部加"上游岗：id-card-main-designer.md"交叉引用
   4.6 用 templates/placeholder-registry.md 登记（走 PR 流程，templates/registry-pr-template.md）
   4.7 用 templates/subtask-list.md 登记小型 routine 工作流
   4.8 用 templates/claim-protocol.md 写入认领协议段
   4.9 用 templates/health-checklist.md 初始化 CHAOSS 健康度挂钩
5. 输出形态判断：
   - 项目根含 .claude/agents/ → 按官方 subagent frontmatter 语法输出（兼容加载）
   - 否则按纯 markdown 输出
6. 八原则自检 + 落盘到项目根 .claude/coordination/
```

—— main 自带任务 / 用户提新需求时 ——

```
7. 任务派发（main 拿到新任务）：
   7.1 读各岗 身份卡的"职责清单"匹配任务能力
   7.2 读匹配岗的"read 最小契约"取缩小版
   7.3 生成派发材料（必含任务描述 / 目标岗身份卡路径 / read 契约缩小版 / REQ-DESIGN 引用）
   7.4 强制行："身份牌：id-card-<昵称>.md（开工前先读，按 read 最小契约执行）"
   7.5 选派发形式（读 references/dispatch-formats.md）：
        - 形式 A 用户复制命令（templates/dispatch-command.md）
        - 形式 B 命令文件落到 .claude/coordination/active/（templates/dispatch-file.md）
   7.6 main 等目标岗走常规工作流；结论（不是原文）回流 main
```

—— 周期触发 ——

```
8. 重议周期触发（每 4 周或每 milestone）：
   - 读 templates/credibility-revisit-protocol.md
   - main 召集、相关 agent 投票、可修改可信度条款但不可删
9. 健康度告警响应（CHAOSS 仪表盘）：
   - 告警响应方式：临时挂起 / 触发分权 / 招新人 / 调用重议
   - 不绕过健康度告警——告警必须响应
```

---

## 2. 常规工作流：领工（任意 agent 视角）

```
0. 接到任务通知（形式 A 用户粘贴 / 形式 B 主动扫 .claude/coordination/active/dispatch-*.md）
1. agent 启动：
   - 开工自检：git fetch + status 干净 + 无 ahead
   - 读 id-card-<本岗昵称>.md（领身份）
   - 读 references/regular-workflow.md（确认本岗完整流程）
2. 拿任务：读 REQ-<id>.md / DESIGN-<id>.md，确认边界/交付物/验收标准
   - 改 dispatch 单 status: dispatched → claimed → in_progress
3. 明确工作内容 / 范围 / 交接点：
   - read 最小契约执行（必读 vs 显式无需读）
   - 红线对照清单
   - **核对派发单与现状**：不符处记进 handoff §派发瑕疵反向纠错（37% 的单会命中）
4. 干活（遇范围外 bug 标 deferred，不顺手做）
5. 交工凭证（强顺序：handoff → status → push）：
   - 本地 commit → 实跑输出（按 dispatch §测试员模式 A/B/C）
   - **落 handoff 文件**（必做；status 变更的强前置）
   - 改 dispatch status: in_progress → handoff（handoff 必须先落盘）
   - git push → 报远程 hash → REQ 回填 → 红线自检
   - 详细步骤见 references/regular-workflow.md 步骤 5
6. 交接：用 templates/handoff-checklist.md 交下一岗
   - 交接材料第一项 = "handoff 文件已落盘"（必做）
   - 材料：handoff + 变更清单 + 测试输出 + commit hash

> **关键约束（v0.2.1 新增）**：implementer 改 status: in_progress → handoff 的**强前置条件** = handoff 文件已落盘。
> 缺 handoff 的 status 变更视为无效，main-designer 不验收。
> 详见 `references/lessons-learned.md`（dispatch-007 RED 现场）+ `templates/dispatch-file.md` §交工凭证。

### 2.1 commit 锚点时序（硬规则，避免自指陷阱）

`git rev-parse --short HEAD` 拿到的是**当前** HEAD。commit 前跑会拿到上一个 commit 的 hash，报进 handoff 就是错的。

```
implementer 干活 → commit → push  ←── 必须先完成
                                  ↓
              verifier 跑测试 / main-designer 验收
```

- implementer commit + push **必须先于** verifier 跑测试
- main-designer 验收**必须在** implementer commit + push 之后
- 违反时症状：handoff 里的 commit hash 对应不到实际改动

### 2.2 串行接单（多单改同一文件时）

多个 dispatch 单命中同一批文件时，implementer **严格串行**，不并行接：

```
claimed → in_progress → commit + push → handoff → 接下一单
```

判定标准：**文件族是否重叠**。不重叠可跨线并行；重叠必须串行。实战 5 单连续改同 2 个文件，串行链路零冲突。

### 2.3 测试员模式 A/B/C（实测数据）

main-designer 在 dispatch 单指定方式，implementer 按指定方式跑并填 handoff §测试员耗时。

| 方式 | 描述 | 实测 |
|---|---|---|
| **A** | implementer 自跑测试 | **稳定 120s ± 30s**（10 次实测） |
| **B** | 派独立验证岗 subagent 跑 | **1359s**（1 次实测，≈ 11× A） |
| **C** | 用 Agent 工具派 subagent 跑 | 未采数据 |

**默认用 A 模式。**

> ⚠ **B 模式已知缺陷**：子进程卡死时 subagent **既不能报告也不能 abort**，只能等 harness timeout 兜底。实测一次卡死 9 分钟。B 模式下 implementer 必须自行排查死锁——subagent 帮不上。B 模式不作为固定模式保留。

### 2.4 探针单模式（小单验证再派大单）

方向不确定、或涉及数据改动可能破坏基线时，先派**最小探针单**验证命令链路与影响面，再决定大单怎么派。

实战价值：一次探针单就纠出了派发方两处字段错误（把坐标当 id 用、引用了错误的队列 API），避免大单返工。

### 2.5 handoff 落盘强前置（v0.2.1 新增）

**铁律**：implementer 改 dispatch status `in_progress → handoff` 之前，必须先落 `.claude/coordination/active/handoff-<id>.md` 文件。缺 handoff 文件的 status 变更视为无效。

**强顺序**（缺一即交接无效）：

```
1. 本地 commit（不 push；commit message 引用 dispatch §验收标准）
2. 实跑输出（PASS/FAIL 计数 + 测试用例清单；按 §2.3 测试员模式）
3. 落 handoff 文件（必做）：
   - 路径：.claude/coordination/active/handoff-<YYYY-MM-DD>-<NNN>.md
   - 模板：templates/handoff-template.md
   - 内容：§完成总结 / §复核点对照 / §归档建议 / §新发现问题 / §接续点 / §测试员耗时 / §派发瑕疵反向纠错 / §红线自检
4. 改 dispatch status：in_progress → handoff（回填交工 commit hash）
5. git push（handoff 落盘 + status 改完后再 push；按 §2.1 commit 锚点时序）
6. 报远程 commit hash + remote branch
7. 回填 REQ 单"已完成 + commit hash + 验收岗"
8. 红线对照自检：dispatch §红线 + id-card §永久不碰清单 全打勾
```

**RED 现场**（dispatch-2026-09-09-007，2026-09-10）：implementer 在 feat commit 2e2c775 闭环后跳过 handoff 落盘直接改 status=handoff，被 main-designer session 启动时拦截，浪费 1 个 session 决策时间。详见 `references/lessons-learned.md`。

---

## 3. 派 subagent 模式（main-designer 同会话直接派活）

```
- 输入：任务描述 + 对应身份卡 read 契约缩小版
- 输出：结论（不是原文）—— pass/fail + 关键证据
- main-designer 只消费结论不重跑（§三 末 认领协议硬条款）
- subagent 走完整 §2 流程，read 范围被缩小
- 与形式 A/B 关系：A/B 是给"目标 agent 不在 main-designer 同一会话"的情况；本模式是同会话直接派
- 独立验证岗用 templates/identity-verifier.md（只读不写 / 不递归派活 / 忠实报告）
- 可选真冗余（未验证）：关键任务启用 N 个不同身份并行投票，读 references/redundancy-validation.md
```

---

## 3.5 session 接续 与 中断恢复

### session 接续点（长会话必备）

会话接近上下文上限、或需要主动关闭时，**关闭前**写 `.claude/coordination/active/resume-point-<task_id>.md`。新 session 启动必读。

必含字段：

| 字段 | 内容 |
|---|---|
| 已闭环 | 单号 + commit hash + 一句话摘要（表格） |
| commit 链 | `hash1 → hash2 → hash3`（便于对账） |
| 当前 todo 状态 | 按 completed / dispatched / pending 分类 |
| 关键发现 | 本轮踩坑与结论，避免下轮重踩 |
| 待拍板选项 | 悬而未决的选项 + 各自代价 |
| 下次启动命令 | 可直接粘贴的第一条命令 |

实战：3 份接续点跨 session 生效，避免了上下文丢失导致的重复劳动。

### 中断恢复三分类（断电 / 会话断）

按 `active/` 里单子的 status 字段分类处理：

| 现场状态 | 判定 | 动作 |
|---|---|---|
| `status=dispatched` + implementer 身份卡 `idle` | implementer 从未接单 | 旧单 mv `archive/` 标 `[stale] abandoned: 中断`，派新 task_id |
| `status=in_progress` + 无 handoff | implementer 在干但断了 | 等续接；超时标 `[stale]` + ping |
| `status=handoff` | 已交付未验收 | 直接续验收（实现产物已 commit + push，不会丢） |

> 实战教训：第一次试用就因断电丢了一整单。**根因是协议状态文件无 git 保护**（见原则 6 的已知冲突）。

---

## 4. 文件加载指引（按需读）

**实战验证过的（优先读）**：

| 何时读 | 读哪个文件 |
|---|---|
| 派发单模板（含 §开工前核对清单）| templates/dispatch-file.md / dispatch-command.md |
| 交接单模板（含 §反向纠错 / §测试员耗时）| templates/handoff-checklist.md |
| handoff 文件模板（含 8 段）| templates/handoff-template.md |
| 独立验证岗身份卡 | templates/identity-verifier.md |
| session 接续点模板 | templates/resume-point.md |
| 常规工作流步骤 | references/regular-workflow.md |
| 派发形式选择 | references/dispatch-formats.md |
| 招什么岗 | references/role-catalog.md |
| 三层结构 | references/three-layer-structure.md |
| 实战经验教训 | references/lessons-learned.md |

**未经实战验证（按需读，小项目可跳过）**：

| 何时读 | 读哪个文件 |
|---|---|
| 8 条原则速查 | references/eight-pinning-principles.md |
| 输入采集 | references/input-collection-checklist.md |
| 规模档位判定 | references/scaling-modes.md |
| 协议走 PR 流程 | references/code-as-registry.md |
| CHAOSS 健康度挂钩 | references/health-metrics.md |
| 可撤销身份机制 | references/identity-revocation.md |
| 真冗余验证 | references/redundancy-validation.md |
| 反向 Conway 教学 | references/conway-inverse-maneuver.md |
| 竞品对比 | references/competitive-landscape.md |

---

## 5. 不做的事

- 不预设具体岗位清单（实现/测试/内容/数值/...）—— 交给 main-designer 按项目需要招聘
- 不绑定任何领域（游戏/Web/移动/数据/...）—— 交给 main-designer 用 role-catalog.md 选用
- 不预生成具体项目的 id-card-*.md 内容（main-designer 拿到模板后自己填）
- 不解决岗位冲突（同文件两个岗要主所有权时由 main-designer 决策）
- 不在 SKILL.md 主体硬写案例（案例只在 examples/README.md 一个文件里匿名化呈现）
- **不派单前不核对现状**——§开工前核对清单 是硬要求（14+ 处实战翻车都出在这）
- **不在 implementer commit + push 前跑验收**（commit 锚点自指陷阱）
- implementer **不擅自拍板设计矛盾**，写进 handoff §疑问 等 main-designer 定
- implementer **不擅自解红线**——红线只能由 main-designer 显式解除
- implementer **不顺手修范围外 bug**——标 deferred
- 原则 5-8 未验证，**不在小项目里默认开启**
- **不在 handoff 落盘前改 status: in_progress → handoff**——v0.2.1 强约束，违反则 status 变更无效

---

## 6. 版本

- **v0.2.1（2026-09-10）**：handoff 落盘强前置约束。修复 dispatch-2026-09-09-007 RED 现场（implementer feat commit 闭环但跳过 handoff 落盘）。
  - SKILL.md §2.5 新增：handoff 落盘 8 步强顺序
  - templates/dispatch-file.md §交工凭证：handoff → status → push 强顺序
  - templates/handoff-checklist.md：交接材料第一项 = handoff 已落盘
  - references/regular-workflow.md 步骤 5：8 步强顺序 + 前置条件
  - templates/handoff-template.md（原 report-template.md 重命名）：handoff 标准模板
  - references/lessons-learned.md（新）：RED 现场 + 修复设计
- **v0.2（2026-09-08）**：首次实战反馈整合（20 单闭环）。
  - frontmatter 修正：删掉非官方字段 `version` / `trigger_words`（不被 Claude Code 识别，触发词并入 `description`）
  - 结构对齐实战口径：`roles/inbox/` → `.claude/coordination/active/`，`IDENTITY-<岗>.md` → `id-card-<昵称>.md`
  - 原则 1-4 标注已验证，5-8 标注未验证 + 小项目默认关闭
  - 新增实战机制：commit 锚点时序 / 串行接单 / 测试员模式 A/B/C 实测 / 探针单 / session 接续点 / 中断恢复三分类 / 反向纠错 37% 数据
  - 新增模板：`identity-verifier.md`、`resume-point.md`
  - handoff 补 §测试员耗时 段（修 v0.1 的自指断裂——dispatch 模板指向了一个不存在的段）
- **v0.1（试用版，2026-09-02）**：完整 8 条原则 + 30 文件落盘（未经实战）。
