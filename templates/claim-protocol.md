# 认领协议段落模板（§三 末）

> 嵌入项目根 `.claude/coordination/CONVENTIONS.md` 或主文档协议段。任何 agent 开工必须先按此协议。

## 协议内容

```markdown
# 认领协议（Claim Protocol）

## 1. REQ 单模板强制行

每张 REQ 单第一行强制包含：

> 身份牌：id-card-<昵称>.md（开工前先读，按 read 最小契约执行）

无此行的 REQ 单视为无效。

## 2. 用户转达命令第一行

当 main 或目标 agent 收到用户转达的命令时，第一行强制为：

> 先读 id-card-<昵称>.md，按 read 范围执行

## 3. 派 subagent 时附 read 契约缩小版

main 派 subagent 时，必须把目标岗 身份卡的 read 最小契约**缩小版**直接塞给 subagent：

```
read 最小契约（缩小版）：
- 必读：<本任务相关文件>
- 显式无需读：<其他岗设计稿 / 归档 / 视图层>
```

main 只消费结论（pass/fail + 关键证据），不消费原文。

## 4. 设备轮换时的认领

设备 / 会话轮换时，"谁接单谁认领"：

```
1. 把本会话的 身份卡复制到新会话
2. 读占位登记表确认身份状态
3. 在 REQ 单回填"已认领 + 当前 commit hash"
```

## 5. 协议即代码（v6）

本协议任何变更走 GitHub PR，模板 templates/registry-pr-template.md。

## 6. 重议与告警（v6）

- 可信度条款重议：templates/credibility-revisit-protocol.md（每 4 周或每 milestone）
- 健康度告警响应：templates/health-checklist.md（必响应）
```

---

## 不要做的事

- 不要省 REQ 单的"身份牌"行——无行即无效
- 不要让派 subagent 时只给"任务描述"——必须附 read 契约缩小版
- 不要在协议变更时跳过 PR 流程——v6 协议即代码