# Changelog

## 0.2.0 (2026-09-08)

首次实战反馈整合。数据来源：某项目试用 20 单闭环（19 单走完 handoff）。

### 修阻塞项

- **frontmatter 修正**——删掉 `version` 与 `trigger_words` 两个字段。这两个都**不是 Claude Code 识别的官方字段**（对照全机 60+ 个可正常加载的 skill，frontmatter 只用 `name` + `description`）。`trigger_words` 里那 8 个中文触发词写了等于没写，已并入 `description` 自然语言——真正决定 skill 是否被加载的是 `description`。
- **CHANGELOG 补记**——上一次改动（3 个模板 +42 行）只推了代码没记 CHANGELOG，导致从版本历史看像"只发布过一次"。本次补齐。

### 结构对齐实战口径

从未运行过的口径 → 20 单实测跑顺的口径：

| 原 | 改为 |
|---|---|
| `roles/inbox/` + `roles/outbox/` | `.claude/coordination/active/`（dispatch 与 handoff 同目录共存） |
| `IDENTITY-<岗名>.md` | `id-card-<昵称>.md` |
| `IDENTITY-main.md` | `id-card-main-designer.md` |

### 诚实标注验证状态

- SKILL.md §0 拆为「原则 1-4 已实战验证」与「原则 5-8 未验证，小项目默认关闭」
- 9 个 references 顶部加未验证声明（health-metrics / redundancy-validation / competitive-landscape / conway-inverse-maneuver / scaling-modes / code-as-registry / identity-revocation / input-collection-checklist / eight-pinning-principles）
- 原则 6（协议即代码）补已知冲突警告：若项目 `.gitignore` 排除了协议状态文件所在目录，本原则没有可执行载体，commit 锚点全部失效。实战因此丢过一整单。

### 新增实战机制

- **反向纠错实测数据**——20 单里 7 单（37%）发生反向纠错，共 16 处，触发 4 次 main-designer 回头改模板。这是本协议被验证过的最大价值。
- **commit 锚点时序**——implementer commit + push 必须先于 verifier 跑 / main-designer 验收。commit 前取 hash 会拿到上一个 commit（自指陷阱）。
- **串行接单**——多单改同一批文件时严格串行，判定标准是文件族是否重叠。实战 5 单连续改同 2 文件零冲突。
- **测试员模式 A/B/C 实测**——A（自跑）稳定 120s ± 30s / 10 次；B（派 verifier subagent）1359s / 1 次 ≈ 11× A。默认用 A。
- **session 接续点**——长会话关闭前写 `resume-point-<id>.md`，新 session 必读。3 份实战验证。
- **中断恢复三分类**——按 `active/` 里单子 status 字段判定：`dispatched`+idle 弃单重派 / `in_progress` 等续接 / `handoff` 直接续验收。
- **探针单模式**——方向不确定时先派最小探针验证链路，再决定大单。一次探针纠出派发方两处字段错误。
- **派发后 plain edit 修订**——派发后发现单子写错直接改单，不重发不换 task_id。实战 4 次。

### 模板变更

- 新增 `templates/identity-verifier.md`——独立验证岗身份卡（只读不写 / 不递归派活 / 忠实报告 / 验证报告字段 / B 模式死锁缺陷说明）
- 新增 `templates/resume-point.md`——session 接续点模板
- `templates/handoff-checklist.md` **补 §测试员耗时 段**——修 v0.1 的自指断裂：dispatch 模板写了"写进 handoff 的 §测试员耗时 段"，但 handoff 模板里根本没这个段。另补 §已知未做（deferred）/ §疑问 / §main-designer 回应 三段。
- `templates/dispatch-file.md` / `dispatch-command.md`——§开工前核对清单 从 4 项扩到 5 项（新增「必查本需求的历史派发单」）；§测试员模式要求 补实测数据与 B 模式死锁警告；补 §文件所有权 / §串行并行 / §拍板 段

### 已知未修

- **跨机器 file sync**——当前协议只适用于同一文件系统。跨机器需要 sync 机制，超出协议范围。
- **§开工前核对清单 仍不能完全防住派发瑕疵**——迭代 7 轮后每轮新单仍会暴露新的不符点。清单能降低发生率，不能归零。

## 0.1.0 (2026-09-02)

试用版首推。

- **8 条原则** 默认全开（身份是单位 / 占位不养空卡 / 职责隔离 / main 默认 / 规模自适应 / 协议即代码 / 可信度写死+重议 / 健康度挂钩）
- **28 个文件** 落盘（v0.2 增至 33）：
  - 1 个 SKILL.md（主流程）
  - 13 个 templates（identity / 招聘 / 派发 / 登记 / 任务 / 协议 / 测试可信度 / 交接 / 健康度 / 重议 / PR 模板）
  - 13 个 references（原则速查 / 角色目录 / 工作流 / 派发形式 / 三层结构 / 输入采集 / 竞品对比 / Conway / 规模自适应 / 协议即代码 / 健康度指标 / 撤销机制 / 真冗余）
  - 1 个 examples/README.md
- **调研依据**：
  - AI agent 圈层：anthropics/skills 空白 / context-aware-delegation 差异化
  - 经典项目管理：Conway's Law / Scrum / Team Topologies / Shape Up / Stripe RFS
  - 大型工程实践：Linux MAINTAINERS / Kubernetes OWNERS / CHAOSS 指标
  - 反向案例：Linux 2018 / XZ Utils 2024 / CDPR Crunch / Valve 隐性等级 / RACI 膨胀
- **v0.1 → v1.0 路线**：基于实际项目试用反馈，迭代改进