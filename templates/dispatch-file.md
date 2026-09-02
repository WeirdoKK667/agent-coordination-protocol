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