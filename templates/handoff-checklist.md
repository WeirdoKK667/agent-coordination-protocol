# 跨岗交接清单（常规工作流步骤 6 用）

> 实现 → 验证、验证 → main、main → 用户——所有跨岗交接都走这个清单。

## Schema

```markdown
# 交接清单 — <交接 ID>

## 交接双方

- 交方：id-card-<昵称 A>.md
- 接方：id-card-<昵称 B>.md

## 交接时间

<YYYY-MM-DD HH:MM>

## 交接材料（必含；缺一即交接无效）

- [ ] **handoff 文件已落盘**（必做；status: in_progress → handoff 的强前置条件）—— `.claude/coordination/active/handoff-<id>.md`
- [ ] 变更清单（改了哪些文件 / 行数 / commit hash）
- [ ] 实跑输出（PASS/FAIL 计数 + 测试用例清单）
- [ ] 远程 commit hash（不是本地 hash；必须 push 后取）
- [ ] REQ 单回填（已完成 + commit hash + 验收岗）
- [ ] 红线对照自检（确认没越界）

## 交接原因

<一段话讲为什么交接 / 下一步要做什么>

## 派发瑕疵反向纠错（如适用）

implementer 接单后，发现 dispatch 与现状不符时**必填**。实战 37% 的单会命中这一段。

- 派发单哪条与现状不符：<引用 dispatch 段>
- implementer 的判断依据：<文件路径 + commit hash + 当前内容>
- main-designer 是否接受纠错：<待 main-designer 验收时回填>

## 测试员耗时（dispatch §测试员模式要求 指定方式时必填）

- 开始时间：<ISO8601>
- 测试命令：`<完整命令文本>`
- 输出 PASS/FAIL 数：<n>/<m>
- 结束时间：<ISO8601>
- 总耗时：<秒>
- subagent 启停耗时（B / C 模式适用）：<秒>
- 感受：<一句话——本单是否值得为验证单独开 subagent>

## 已知未做（deferred）

范围外发现的问题**标记不做**，不顺手修：

- <问题 + 为什么归为范围外 + 建议后续怎么处理>

## 疑问 / 设计矛盾（请 main-designer 拍板）

遇设计矛盾**不擅自拍板**。给出选项 + 倾向 + 理由：

- **<问题>**：选项 α <代价> / 选项 β <代价> —— 倾向 <α/β>，理由 <…>

## main-designer 回应（验收时回填）

> 交方写完 handoff 后留空此段；main-designer 验收时直接在 handoff 内回复上面的 §疑问，不另开文件。

- 针对 §疑问 1：<拍板结论>
- 针对 §疑问 2：<拍板结论>
- 反向纠错是否接受：<逐条回应>
- 整体评价：accepted / rejected + 理由

## 接方确认

- [ ] 已读交接材料
- [ ] 已开工自检（git fetch + status 干净）
- [ ] 已读 id-card-<昵称 B>.md
- [ ] 已开始走常规工作流

## 接方反馈（完成时回填）

- 结论：pass / fail / 需补充
- 关键证据：<commit hash / 实跑输出 / 失败日志>
- 后续：<下一步动作>
```

---

## 渲染示例（实现 → 验证）

```markdown
# 交接清单 — handoff-001

## 交接双方

- 交方：id-card-impl-module-a.md
- 接方：id-card-verifier.md

## 交接时间

2026-09-02 15:30

## 交接材料

- [x] 变更清单：`src/module_a/foo.ts` +120 / -30（commit abc1234）
- [x] 实跑输出：Total 81 | Passed 81 | Failed 0
- [x] 远程 commit hash：abc1234 on origin/main
- [x] REQ 单回填：REQ-001 已完成 + abc1234 + verifier
- [x] 红线对照：未触碰 `core/state.json`

## 接方确认

- [x] 已读交接材料
- [x] 开工自检：git fetch OK / status 干净
- [x] 已读 id-card-verifier.md
- [x] 开始走常规工作流

## 接方反馈

- 结论：pass
- 关键证据：commit abc1234 verified, 81/81 测试通过
- 后续：通知 main 收工
```

---

## 不要做的事

- 不要省交接材料中的任何一项——缺一项交接即无效
- 不要让接方在未开工自检前开工——会带入脏工作区
- 不要把本地 commit hash 当远程 hash 报——必须 push 后取 hash
- 不要在 commit + push 之前取 hash——`git rev-parse HEAD` 会拿到上一个 commit（自指陷阱）
- 不要把范围外发现的 bug 顺手修掉——标进 §已知未做
- 不要擅自拍板设计矛盾——写进 §疑问 等 main-designer 回应
- 不要删掉 §main-designer 回应 段——它是验收回填位，空着也要留