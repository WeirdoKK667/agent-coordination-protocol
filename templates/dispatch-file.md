# 派发命令文件模板（形式 B：dispatch-<id>.md）

> 落到项目根 `.claude/coordination/active/` 或约定的共享目录。目标 agent 自己读文件后启动常规工作流。适合 agent 自己能发现任务（inbox 轮询 / 文件监听）。

## 文件名约定

```
.claude/coordination/active/dispatch-<YYYY-MM-DD>-<任务简短标识>.md
```

## 文件内容模板

```markdown
# dispatch-<id>

**派发时间**：<YYYY-MM-DD HH:MM>
**派发人**：id-card-main-designer.md
**目标岗**：id-card-<昵称>.md
**状态**：dispatched → claimed → in_progress → handoff
**优先级**：high / medium / low

---

## 身份牌（先读）

**身份牌**：id-card-<昵称>.md（开工前先读，按 read 最小契约执行）

## 任务描述

<一段话讲清任务目标 / 边界 / 交付物>

## 开工前核对清单（main-designer 派发前必查 5 项）

> **这不是可选项。** 实战 14+ 处翻车全部出在派发前没做实地核验——凭单子规范推断"缺什么"，结果派的东西早就存在。

- [ ] 必读现状文件：<列具体文件，逐个真读过，不要按规范推断>
- [ ] 必查基线数字：<跑基线测试拿当前值，写到 §验收；过期基线会导致验收对不上>
- [ ] 必查红线文件状态：<已归档 vs 进行中，避免派已闭环的单>
- [ ] 必查文件所有权清单：<含测试前置数据文件——新增断言 ID 时可能要登记>
- [ ] 必查本需求的历史派发单：<同一 REQ 是否已有单闭环过，避免重复派>

## 测试员模式要求

main-designer 指定 implementer 走 A / B / C 中哪种方式跑测试：

| 方式 | 描述 | 实测 |
|---|---|---|
| **A** | implementer 自跑测试 | **稳定 120s ± 30s**（10 次） |
| **B** | 派独立验证岗 subagent 跑（templates/identity-verifier.md） | **1359s**（1 次，≈ 11× A） |
| **C** | 用 Agent 工具派 subagent 跑 | 未采数据 |

**本单指定方式**：<A / B / C —— 默认 A>

> ⚠ **B 模式已知缺陷**：子进程卡死时 subagent 既不能报告也不能 abort，只能等 harness timeout 兜底（实测卡死 9 分钟）。选 B 时 implementer 必须自行准备排查死锁的手段。B 模式不作为固定模式保留。

implementer 采字段写进 handoff §测试员耗时 段：开始时间 / 命令 / PASS-FAIL 数 / 结束时间 / 总耗时 / subagent 启停耗时 / 主观感受。

## 验收标准（必须可验证）

1. <可验证的检查项——写完整命令，含必要的参数；默认参数可能只跑子集>
2. <可验证的检查项>

## 拍板（派发方预先定，避免 implementer 卡住）

- **<可预见的争议点>**：拍板 <选项> —— 理由 <…>

## 任务凭证

- REQ 单：REQ-<id>.md
- DESIGN 文档：DESIGN-<id>.md（如有）

## read 最小契约（缩小版）

- 必读：本卡 + REQ 单 + <具体改动文件> + <断言对应测试段>
- 显式无需读：<其他岗设计稿 / 归档 / 视图层>

## 红线

- 不写 `<永久不碰的文件>`
- 不调用 `<未授权的跨模块 API>`
- 遇范围外 bug 标 deferred，不顺手修
- 红线只能由 main-designer 显式解除——implementer 不擅自解

## 文件所有权

| 文件 | 本单可碰 | 备注 |
|---|---|---|
| <路径> | ✓ / ✗ | <测试前置数据文件也要列——新增断言 ID 时需登记> |

## 串行 / 并行

- **本单与 <单号> 改同一批文件** → 严格串行：claimed → in_progress → commit + push → handoff → 接下一单
- 文件族不重叠 → 可跨线并行

## 交工凭证（强顺序：handoff → status → push）

> **铁律（v0.2.1 新增）**：implementer 改 dispatch status `in_progress → handoff` 的**强前置条件** = `.claude/coordination/active/handoff-<id>.md` 已落盘。
> 没有 handoff 文件的 status 变更视为无效，main-designer 不验收。
> RED 现场：dispatch-2026-09-09-007 implementer feat commit 闭环但跳过 handoff 落盘。详见 `references/lessons-learned.md`。

按以下顺序执行，缺一即交接无效：

1. **本地 commit**（不 push；commit message 引用 dispatch §验收标准）
2. **实跑输出**：PASS/FAIL 计数 + 测试用例清单（按 dispatch §测试员模式 A/B/C）
3. **落 handoff 文件**（必做）：`.claude/coordination/active/handoff-<YYYY-MM-DD>-<NNN>.md`
   - 模板见 `templates/handoff-template.md`（含 §完成总结 / §复核点对照 / §归档建议 / §新发现问题 / §接续点 / §测试员耗时 / §派发瑕疵反向纠错 / §红线自检）
   - 缺此文件 → status 变更无效，main-designer 不验收
4. **改 dispatch status**：`in_progress → handoff`（回填交工 commit hash）
   - 前置条件：handoff 文件必须已落盘（步骤 3）
5. **git push**（handoff 落盘 + status 改完后再 push；按 §2.1 commit 锚点时序）
6. **报远程 commit hash + remote branch**
7. **回填 REQ 单**："已完成 + commit hash + 验收岗"
8. **红线对照自检**：dispatch §红线 + id-card §永久不碰清单 全打勾

## 交接

handoff 落盘 + status=handoff + push 后，按 templates/handoff-checklist.md 交给 <下一岗>

## 状态

- [ ] 已读身份卡
- [ ] 已开工
- [ ] handoff 文件已落盘（必做；status 变更的前置条件）
- [ ] status: in_progress → handoff（回填 commit hash）
- [ ] git push 完成
- [ ] 已交工
- [ ] 已交接

## RED 现场（dispatch-007 教训）

implementer 在 `feat commit` 闭环后跳过 handoff 落盘直接改 status，被 main-designer 验收拦截。
后续 implementer 必须把"handoff 落盘"当作 commit 后的**强制下一步**，不是可选项。
```

---

## 目录约定

```
.claude/coordination/
├── active/                      # 进行中（dispatch 与 handoff 共存同目录）
│   ├── dispatch-<id>.md
│   ├── handoff-<id>.md
│   └── resume-point-<id>.md     # session 接续点
├── archive/                     # 已完成，带日期前缀便于追溯
│   ├── <YYYY-MM-DD>-dispatch-<id>.md
│   └── <YYYY-MM-DD>-handoff-<id>.md
├── templates/
└── id-card-<昵称>.md
```

dispatch 与 handoff **同目录共存**（不分 inbox / outbox）：一个任务的两份状态文件放一起，扫 `active/` 一眼看清全貌。

## 派发后修订

派发后发现单子写错，**直接 plain edit 改单**，不重新发派、不换 task_id。implementer 按修订版执行。实战 4 次这么干，没出问题。

## 不要做的事

- 不要把 active/ 当聊天记录——只放任务派发与交接，不放自由讨论
- 不要在 dispatch 里写所有 read 契约——只写本任务相关
- 不要让 dispatch 长期滞留 active/——完成后归档到 archive/
- **不要跳过 §开工前核对清单**——这是最高频翻车点
- 不要在 §验收标准 里写省略参数的命令——默认参数可能只跑子集，拿不到全量基线
- **不要在 handoff 落盘前改 status: in_progress → handoff**——v0.2.1 强约束，违反则 status 变更无效
