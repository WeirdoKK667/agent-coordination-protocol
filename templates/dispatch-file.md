# 派发命令文件模板（形式 B：dispatch-<id>.md）

> 落到项目根 `roles/inbox/` 或约定的共享目录。目标 agent 自己读文件后启动常规工作流。适合 agent 自己能发现任务（inbox 轮询 / 文件监听）。

## 文件名约定

```
roles/inbox/dispatch-<YYYY-MM-DD>-<任务简短标识>.md
```

## 文件内容模板

```markdown
# dispatch-<id>

**派发时间**：<YYYY-MM-DD HH:MM>
**派发人**：IDENTITY-main.md
**目标岗**：IDENTITY-<岗名>.md

---

## 身份牌（先读）

**身份牌**：IDENTITY-<岗名>.md（开工前先读，按 read 最小契约执行）

## 任务描述

<一段话讲清任务目标 / 边界 / 交付物>

## 开工前核对清单（main-designer 派发前必查 4 项）

- [ ] 必读现状文件：<列具体文件，不要按单子规范推断>
- [ ] 必查基线 PASS 数：<跑基线测试拿当前值，写到 §验收>
- [ ] 必查红线文件状态：<done/ vs pending/，避免派已归档单>
- [ ] 必查文件所有权清单：<含 autotest 前置数据文件，由 implementer 按项目填>

## 测试员模式要求（推荐补字段，可选）

main-designer 指定 implementer 走 A / B / C 中哪种方式跑测试：

| 方式 | 描述 |
|---|---|
| A | implementer 自跑 autotest |
| B | implementer 派独立验证岗 subagent 跑测试 |
| C | implementer 用 Agent 工具派 subagent 跑测试 |

implementer 采字段：开始时间 / 命令 / PASS-FAIL 数 / 结束时间 / 总耗时 / subagent 启停耗时 / 主观感受
写进 handoff 的 §测试员耗时 段。

## 任务凭证

- REQ 单：REQ-<id>.md
- DESIGN 文档：DESIGN-<id>.md（如有）

## read 最小契约（缩小版）

- 必读：本卡 + REQ 单 + <具体改动文件> + <断言对应测试段>
- 显式无需读：<其他岗设计稿 / 归档 / 视图层>

## 红线

- 不写 `<永久不碰的文件>`
- 不调用 `<未授权的跨模块 API>`

## 交工凭证

- git push + 实跑输出（PASS/FAIL 计数）
- 报 commit hash + remote branch
- 回填 REQ 单"已完成 + commit hash + 验收岗"

## 交接

完成后按 templates/handoff-checklist.md 交给 <下一岗>

## 状态

- [ ] 已读 IDENTITY 卡
- [ ] 已开工
- [ ] 已交工
- [ ] 已交接
```

---

## inbox 目录约定

```
roles/
├── inbox/
│   ├── dispatch-<id>.md
│   └── archive/
│       └── dispatch-<id>.md（已完成归档）
├── outbox/  （目标 agent 交工结论落这里）
└── ...
```

---

## 不要做的事

- 不要把 inbox 当聊天记录——只放任务派发，不放自由讨论
- 不要在 dispatch 里写所有 read 契约——只写本任务相关
- 不要让 dispatch 长期滞留 inbox——完成后归档