# handoff 文件模板（implementer 交工凭证；v0.2.1）

> implementer 改 dispatch status `in_progress → handoff` 的**强前置条件** = 本文件已落盘。
> 文件名约定：`.claude/coordination/active/handoff-<YYYY-MM-DD>-<NNN>.md`（与 dispatch 同序号）。

## 文件名约定

```
.claude/coordination/active/handoff-<YYYY-MM-DD>-<NNN>.md
```

序号与关联 dispatch 一致（dispatch-2026-09-09-007 对应 handoff-2026-09-09-007）。

> **术语说明**：本 skill 上游沿用 v0.2 的 `handoff` 命名。部分项目（如 godot tier0 demo 的 `.claude/acp/`）沿用 `report` 命名——两者指同一对象。本模板以 `handoff` 为准。

## 文件内容模板

```markdown
# handoff-<YYYY-MM-DD>-<NNN>

> 来源 dispatch：dispatch-<YYYY-MM-DD>-<NNN>
> implementer：id-card-<本岗昵称>.md
> 交工时间：<YYYY-MM-DD HH:MM>
> commit：<hash>（远程，非本地；按 SKILL.md §2.1 commit 锚点时序）

## §完成总结

- 变更文件清单：<dispatch §范围里改的文件 + 行数>
- 实跑输出（全量）：PASS ≥ X / FAIL = 0
- 局部断言：<dispatch §验收标准对应断言 PASS 计数>
- DESIGN 修订：<如有，按 design 临时例外授权范围列出>

## §复核点对照

按 dispatch §验收标准 逐项列：

- [ ] a) <验收项 a>：PASS / FAIL（实跑证据）
- [ ] b) <验收项 b>：PASS / FAIL
- ...

## §归档建议（main-designer 拍板执行）

- DESIGN-###：<状态变化 / 不动理由>
- REQ-###：<回填 commit hash / 不动理由 + meta:status=done>
- 旧 dispatch-###：<标 done + close 备注承接关系>

## §新发现问题 / backlog

<独立 REQ 单候选 + 命名建议；无则填「无」>

## §下次 session 接续点

- 强依赖：<如有>
- 待用户拍板：<如有>
- backlog 推进：<如有>

## §测试员耗时（dispatch §测试员模式要求 指定方式时必填）

- 开始时间：<ISO8601>
- 测试命令：`<完整命令文本>`
- 输出 PASS/FAIL 数：<n>/<m>
- 结束时间：<ISO8601>
- 总耗时：<秒>
- subagent 启停耗时（B / C 模式适用）：<秒>
- 感受：<一句话——本单是否值得为验证单独开 subagent>

## §派发瑕疵反向纠错（如适用）

implementer 接单后，发现 dispatch 与现状不符时**必填**：

- 派发单哪条与现状不符：<引用 dispatch 段>
- implementer 的判断依据：<文件路径 + commit hash + 当前内容>
- main-designer 是否接受纠错：<待 main-designer 验收时回填>

## §已知未做（deferred）

范围外发现的问题**标记不做**，不顺手修：

- <问题 + 为什么归为范围外 + 建议后续怎么处理>

## §疑问 / 设计矛盾（请 main-designer 拍板）

遇设计矛盾**不擅自拍板**。给出选项 + 倾向 + 理由：

- **<问题>**：选项 α <代价> / 选项 β <代价> —— 倾向 <α/β>，理由 <…>

## §main-designer 回应（验收时回填）

> 交方写完 handoff 后留空此段；main-designer 验收时直接在 handoff 内回复上面的 §疑问，不另开文件。

- 针对 §疑问 1：<拍板结论>
- 针对 §疑问 2：<拍板结论>
- 反向纠错是否接受：<逐条回应>
- 整体评价：accepted / rejected + 理由

## §红线自检

- [ ] 身份卡 §2 永久不碰清单未越界
- [ ] CLAUDE.md §"协调协议约束" implementer 两硬条款已落实
- [ ] 全量零基线不退（PASS ≥ 基线 / FAIL = 0）
- [ ] dispatch §红线全打勾
- [ ] handoff 文件已落盘（本文件；status 变更的前置条件）
```

## implementer 落 handoff 时机

| 阶段 | 动作 |
|---|---|
| 本地 commit 后 | 准备 handoff 内容（commit hash + 实跑数据） |
| 改 status 前 | **必须先落 handoff 文件**（v0.2.1 强约束） |
| 改 status: in_progress → handoff | 回填交工 commit hash |
| push | handoff + status 都改完后再 push |
| 报远程 hash | push 后取远程 hash（非本地；按 §2.1 时序） |

## main-designer 验收流程

1. **检查 handoff 文件存在**（必做；status 变更的有效性凭证）
2. 读 handoff §完成总结 + §复核点对照
3. 拍板 §归档建议（git mv + 同步索引）
4. 拍板 §新发现问题 / backlog（立新 REQ 单 / 写 backlog）
5. 拍板 §疑问 / §deferred（如有）
6. 派下一个 dispatch 时按 α 简化命令

## 不做的事

- ❌ **不在 feat commit 后直接改 status: in_progress → handoff**（v0.2.1 强约束；必须先落 handoff）
- ❌ 不在 handoff §main-designer 回应段 写 main 决策（main 活）
- ❌ 不重写 dispatch §范围 / §红线（implementer 工作流固化）
- ❌ 不改 DESIGN-### / REQ-###（身份卡 §2 永久不碰）
- ❌ 不跳过 §复核点对照（缺一即视为交工不完整）
- ❌ 不写本地 commit hash 当远程 hash（必须 push 后取）

## 关联

- `dispatch-file.md` §交工凭证：强顺序约束
- `handoff-checklist.md` 交接材料第一项：handoff 文件已落盘
- `regular-workflow.md` 步骤 5：8 步强顺序
- `SKILL.md` §2.5：handoff 落盘强前置约束
- `references/lessons-learned.md` RED 现场（dispatch-2026-09-09-007）
