# 跨岗交接清单（常规工作流步骤 6 用）

> 实现 → 验证、验证 → main、main → 用户——所有跨岗交接都走这个清单。

## Schema

```markdown
# 交接清单 — <交接 ID>

## 交接双方

- 交方：IDENTITY-<岗名 A>.md
- 接方：IDENTITY-<岗名 B>.md

## 交接时间

<YYYY-MM-DD HH:MM>

## 交接材料（必含）

- [ ] 变更清单（改了哪些文件 / 行数 / commit hash）
- [ ] 实跑输出（PASS/FAIL 计数 + 测试用例清单）
- [ ] 远程 commit hash（不是本地 hash）
- [ ] REQ 单回填（已完成 + commit hash + 验收岗）
- [ ] 红线对照自检（确认没越界）

## 交接原因

<一段话讲为什么交接 / 下一步要做什么>

## 接方确认

- [ ] 已读交接材料
- [ ] 已开工自检（git fetch + status 干净）
- [ ] 已读 IDENTITY-<岗 B>.md
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

- 交方：IDENTITY-impl-module-a.md
- 接方：IDENTITY-verifier.md

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
- [x] 已读 IDENTITY-verifier.md
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