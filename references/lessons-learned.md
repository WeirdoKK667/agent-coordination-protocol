# 经验教训（lessons-learned）

> 记录实际项目试用中的 RED 现场 + 修复设计思路，供后续 v0.3+ 协议升级参考。

## v0.2.1：dispatch-007 没落 handoff（2026-09-10）

### RED 现场

- **项目**：godot 4.7.2 + GDScript tier0 demo
- **dispatch**：dispatch-2026-09-09-007（REQ-CONTAINER-UI-001 容器选择 + 互动 UI）
- **失败**：
  - implementer 在 commit `2e2c775` feat 闭环了 dispatch-007 的全部交付物（5 个 .gd 文件 + DESIGN-024a v0.2 修订）
  - implementer **没写** `.claude/coordination/active/handoff-2026-09-09-007.md`（项目用 `report-` 命名，本质同一对象）
  - main-designer 在 commit `32f7935` 把 README 改成"待验收"是 main-designer 的**预期状态**，不是 implementer 实际状态
  - commit `353721a` 后纠正 README "dispatch-007 imp 未接" 备注
- **根因诊断**：
  1. `status: in_progress → handoff` 状态机没有强前置条件
  2. v0.2 的 `templates/dispatch-file.md` §交工凭证 列了 git push / 实跑 / commit hash / REQ 回填 / 红线自检，但**没列 handoff 落盘**
  3. v0.2 的 `templates/handoff-checklist.md` 交接材料 6 项不含"handoff 文件已落盘"
  4. v0.2 的 `references/regular-workflow.md` 步骤 5 顺序松散（git push 在最前，handoff 缺失无拦截）
  5. 跨设备 skill 版本不一致：本机有 v0.1.0 试用版，未及时同步到 remote v0.2.0
- **协调后果**：
  - main-designer session 启动时发现 handoff-007 不存在，必须决策验收策略（A 让 implementer 补 / B 直接基于 commit 验收 / C 部分验收）
  - 浪费 1 个 session 决策时间
  - 暴露"implementer session 结束没 checklist"的协调漏洞
  - 触发了本 skill 的 v0.2.1 增量修复

### GREEN 修复（v0.2.1，2026-09-10）

5 文件改 + 2 新增 + 1 重命名：

| 文件 | 改动 |
|---|---|
| `templates/dispatch-file.md` §交工凭证 | 重写为 8 步强顺序（commit → 实跑 → handoff → status → push → hash → REQ → 红线） |
| `templates/handoff-checklist.md` 交接材料 | 第一项加 "handoff 文件已落盘"（必做） |
| `references/regular-workflow.md` 步骤 5 | 改为 8 步强顺序，前置条件写死 |
| `SKILL.md` §2.5（新增小节） | "handoff 落盘强前置" 约束 |
| `CHANGELOG.md` | v0.2.1 条目 |
| `templates/handoff-template.md`（原 report-template.md 重命名） | handoff 文件标准模板 |
| `references/lessons-learned.md`（本文件） | RED 现场 + 修复设计 |

### REFACTOR：仍可能存在的漏洞

- **多 implementer session 并行**：dispatch status 字段是单一文件，多 implementer 写时可能冲突——需 §race-condition 治理
- **implementer session 崩溃**：handoff 写到一半 implementer session 死掉，status 卡在 in_progress——需 §recovery 流程
- **handoff 内容质量**：implementer 可能落"应付式 handoff"（只填占位）——需 §质量门槛（实跑数据强制）
- **跨设备同步延迟**：implementer 在设备 A 改 status，main-designer 在设备 B 看到旧 status——本质是 git 推送延迟问题，协议层无法根治
- **handoff 落盘后 commit 失败**：handoff 落盘了但 commit 失败，status 已是 handoff 但远程代码缺失——需 §commit-after-handoff 异常路径

### 验证方式

- v0.2.1 修复部署后，下次 dispatch（如 dispatch-2026-09-09-009+）必须强制走新流程
- 验证指标：implementer feat commit 后 24 小时内必须有 handoff-###.md 落盘
- 失败模式：发现 implementer feat commit 但 handoff 缺失 → v0.2.2 进一步加约束（如 commit-msg hook 拦截）

### 跨设备同步教训（用户诊断）

> 用户原话："本次 007 没有 report 就是 skill 未更新到对方设备造成的"

本机 session 启动时，本地 skill 是 v0.1.0 试用版（commit ba6cb4d），而 remote 已推 v0.2.0（commit b9cbc16，包含 20 单实战反馈整合）。本机 rebase 后才看到 v0.2.0 已加 §开工前核对清单 5 项、commit 锚点时序、串行接单等约束——但 v0.2.0 没有显式的 handoff 落盘强约束。

**结论**：
- v0.2.0 → v0.2.1 的增量修复必须在 push 后让对方设备同步
- 跨设备 sync 机制超出 skill 范围（v0.2.0 "已知未修"段已声明）
- 用户侧的体验："skill 未更新到对方设备"——需要让对方设备的 implementer session 在开工前 `git pull` skill 仓库

## 通用经验

### 协议变更时同步

任何 protocol 修订必须同步：
- 全局 skill（`~/.claude/skills/agent-coordination-protocol/`）
- 项目级入口（`.claude/acp/` 8 文件）
- 项目级协议（`.claude/coordination/CONVENTIONS.md`）
- 项目级 README（如 `inbox/README.md` 引用流程的）

只改一处 = 协议漂移。

### RED 现场记录价值

每发现一次协调漏洞（implementer / main-designer / artist 不按流程），必须：
1. 记录 RED 现场（哪条 dispatch / 哪个 commit / 哪个岗 / 什么行为）
2. 找根因（哪个文件哪条约束没写死）
3. 设计修复（哪些文件加前置条件）
4. 部署（commit + push 全局 skill）
5. 留痕（本文件 + CHANGELOG）

不写 lessons-learned = 下次还会踩同一个坑。

### TDD for skills

- RED：跑一次 implementer session 看它违反哪条约束
- GREEN：写修复（前置条件 + 模板 + checklist）
- REFACTOR：找新漏洞（race / crash / quality / sync）
- 验证：下次 implementer session 必走新流程

跟代码 TDD 一致——skill 是协议代码，不是文档。

### 术语映射（v0.2.1 起）

| skill 上游（v0.2） | 项目级（godot tier0 demo） | 含义 |
|---|---|---|
| `handoff-<id>.md` | `report-<id>.md` | 交工凭证 |
| `.claude/coordination/active/` | `.claude/coordination/inbox/` | 进行中状态文件目录 |
| `id-card-<昵称>.md` | `IDENTITY-<岗名>.md` | 身份卡 |
| `main-designer` | `main-designer` | 主设计 |
| `implementer` | `implementer` | 实现 |
| `status: in_progress → handoff` | `status: claimed → done` | 状态转换 |
| `dispatch §验收标准` | `dispatch §复核点` | 派发单验证项 |
| `dispatch §测试员模式要求` | （未显式分模式） | 测试方式选择 |

skill 上游以 v0.2 命名为准；项目级沿用历史命名但语义对齐。
