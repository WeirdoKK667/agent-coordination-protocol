# 协议即代码（references/code-as-registry.md）

> ⚠ **未经实战验证**（截至 2026-09-08）。本文件描述的机制在唯一试用项目里从未触发——该项目规模为 1 人 + 2 终端 + 3 身份卡，按 SKILL.md §0 原则 5 直接落进小项目旁路模式。小项目可跳过本文件。

> v6 原则 6。借鉴 Linux MAINTAINERS + Kubernetes OWNERS 模型。登记表与代码同仓、可版本化、可审计、可回滚。

## 来源模型

### Linux MAINTAINERS 文件

来源：[https://kernel.org/doc/MAINTAINERS](https://kernel.org/doc/MAINTAINERS)

机制：
- 每个子系统有 MAINTAINERS 文件
- 列 maintainer + reviewer + 邮件
- 文件本身随代码 commit 进 kernel tree
- 修改走 patch + reviewer approve

### Kubernetes OWNERS 文件

来源：[https://github.com/kubernetes/community/blob/master/contributors/guide/owners.md](https://github.com/kubernetes/community/blob/master/contributors/guide/owners.md)

机制：
- 每个目录有 OWNERS 文件
- 列 owners + approvers + reviewers
- GitHub PR 时自动 @ 这些人 review
- 修改走 GitHub PR + review approve

## 对应本 skill

### 占位登记表

```
.claude/coordination/
├── registry/
│   ├── placeholder-registry.md（占位登记表）
│   ├── subtask-list.md（次级任务清单）
│   ├── credibility-revisit-<date>.md（重议记录）
│   └── decisions/（决策存档）
└── inbox/（dispatch 文件）
```

所有变更走 GitHub PR + reviewer approve（templates/registry-pr-template.md）。

### 协议版本化

- 协议变更 = Git commit
- 协议回滚 = Git revert
- 协议审计 = git log + git blame
- 协议分支 = 不同项目分叉时按 fork 处理

### 协议即代码的 4 个好处

1. **可审计**：每个变更都有 commit 记录
2. **可回滚**：改坏了可以 revert
3. **可分叉**：项目分叉时协议自然 fork
4. **可对比**：git diff 看变更细节

## 强制要求

- 任何协议变更（登记表 / 任务清单 / 可信度条款 / 健康度阈值）必须走 PR
- 突破 ≤ 6 硬上限的变更必须 2 个 reviewer（co-main 或可信岗）
- 删除可信度硬条款的变更**直接拒绝**（红线）
- 健康度告警未响应的变更**直接拒绝**

## 工具推荐

- **GitHub / GitLab / Gitea**：PR + review 工作流
- **pre-commit hook**：自动检查协议格式
- **GitHub Action**：自动算 CHAOSS 健康度
- **Issue 模板**：模板化 PR / Issue 描述

## 反例

| 案例 | 教训 |
|---|---|
| RACI 矩阵在 Excel 里 | 改了谁都不知道 → 失败 |
| org chart 在 PowerPoint | 跟不上代码变更 → 失败 |
| 协议在 Confluence | 散落各处 → 无人审计 |
| 协议在脑子里 | 改了就忘 → 失败 |

## 与 v6 原则的关系

- 原则 6 直接对应本参考
- 原则 7（可信度写死 + 强制重议）的重议记录 = 协议即代码的产物
- 原则 8（健康度挂钩）的告警响应记录 = 协议即代码的产物