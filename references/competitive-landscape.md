# 竞品对比（references/competitive-landscape.md）

> 2026-09-02 派 subagent 完成。防止 main 第一次用时跟其他方案混淆。

## 关键发现

- **anthropics/skills 官方仓库完全空白**：18 个 skill 无 role/team/identity 类
- **三条原则在社区里都是全新或全新组合**
- **底层载体**：Claude Code 官方 subagent（markdown + YAML frontmatter + system prompt）

## 最像的先例与差异点

| 先例 | URL | 与我们的差异 |
|---|---|---|
| context-aware-delegation Identity Card | [eliteai.tools](https://eliteai.tools/agent-skills/context-aware-delegation) | 手写卡片 vs 我们按模块/协议/根结构派生 |
| yo61/agent-team-topologies | [github.com/yo61](https://github.com/yo61/agent-team-topologies) | 有 test-reviewer 但没有可信度条款 |
| obra/superpowers | [github.com/obra](https://github.com/obra/superpowers) | 强调 review 机制，不解决占位 + 认领 + 可信度 |
| Naresh Dawer Multi-Agent Dev Squad | [LinkedIn](https://www.linkedin.com/posts/naresh-dawer_ai-devops-coding-activity-7446921258966917120-asI0) | tester 无 edit 隔离，思路吻合但无条款化 |
| CSDN "岗位说明书" 概念 | [blog.csdn.net](https://blog.csdn.net/qq_34599132/article/details/162692270) | 概念相似，但无占位/登记/认领/可信度四件套 |
| Claude Code 官方 subagent | [code.claude.com](https://code.claude.com/docs/en/sub-agents) | 底层载体，必须兼容 frontmatter 语法 |

## 我们填补的空白（对比维度表）

| 维度 | 现有方案 | 我们的差异 |
|---|---|---|
| 身份持久化 | 官方 subagent markdown 卡 | 项目级一次生成 N 张 |
| Identity Card 模式 | context-aware-delegation | 按模块/协议/根结构派生，不是手写 |
| 占位 + 认领 | Praison AI / Salesforce claim 模型 | 项目规划期占位，不养空卡 |
| 测试独立成岗 | yo61 / AutoGen / Naresh Dawer | 可信度写死（确定性 pass/fail + 覆盖率） |
| 模块 → 职责 | 无 | 结构化派生 |
| 协议段 | 无 | 岗卡内"认领协议段 + 测试可信度段" |

## 一句话定位

> 唯一一个把"项目结构作为输入 → 自动派生出 N 张持久岗卡（含占位登记表 + 次级任务清单 + 认领协议 + 测试可信度 + 健康度挂钩 + 重议机制）"的产品化方法。

## 风险点 / 差异化

- **context-aware-delegation 是最直接潜在竞争者**——我们按"派生"vs"手写"区分
- **obra/superpowers 生态可能产生张力**——我们是 superpowers 之上的"身份体系"层，互补
- **官方 subagent 机制演进**——frontmatter 字段可能需要跟进

## 三个原则的社区定位

1. 「身份是单位、模块降级为职责内容」—— **全新**
2. 「验证类岗独立、可信度写死」—— **全新组合**
3. 「占位岗不提前养空卡」—— **全新**

加上 v6 调研新增的：
4. 「协议即代码」—— 借鉴 Linux MAINTAINERS + Kubernetes OWNERS
5. 「规模自适应 + ≤ 6 硬上限」—— 借鉴 Brooks + Dunbar + n(n-1)/2
6. 「健康度挂钩 + 告警响应」—— 借鉴 CHAOSS 项目
7. 「可信度写死 + 强制重议」—— 借鉴 Stripe RFS
8. 「main 身份预填 + 工作负载上限」—— 借鉴 AWS STL + Linus 2018 教训