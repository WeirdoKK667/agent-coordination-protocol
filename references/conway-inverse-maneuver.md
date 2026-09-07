# Conway's Law + Inverse Conway Maneuver（references/conway-inverse-maneuver.md）

> ⚠ **未经实战验证**（截至 2026-09-08）。本文件描述的机制在唯一试用项目里从未触发——该项目规模为 1 人 + 2 终端 + 3 身份卡，按 SKILL.md §0 原则 5 直接落进小项目旁路模式。小项目可跳过本文件。

> 我们的"项目结构 = 模块清单 + 协议 + 根结构"输入锚点 = Inverse Conway Maneuver 的工程化版本。

## Conway's Law（1968）

**原文**（Melvin Conway）：
> Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations.

**翻译**：
> 设计系统的组织，产出的系统设计必然是组织沟通结构的副本。

## 推论

- 系统架构 = 组织架构的镜像
- 反之：想要什么架构，先设计什么组织

## Inverse Conway Maneuver

**做法**：先画目标代码 / 模块结构，再倒推团队 / agent 分工。

**对应本 skill**：
- 输入"项目结构（模块清单 / 协议 / 根结构）" → 实际是"目标代码结构"
- 输出 身份卡 → 实际是"派生出的 agent / 组织"
- 因此本 skill 的整套流程 = Inverse Conway 的工程化

## 实施步骤

```
1. 画目标代码结构：
   - 模块边界（哪些是独立模块）
   - 数据流（模块间数据怎么传）
   - 共享文件（哪些文件多模块共用）
2. 倒推 agent 分工：
   - 每个模块配 1 个实现岗（实现类）
   - 跨模块的事务配架构岗
   - 数据 / 内容的事务配数据内容岗
3. 校对：
   - 系统架构是否反映 agent 分工？
   - 沟通路径数（n(n-1)/2）是否在合理范围？
```

## 反 Conway 检查（必须做）

大型项目（> 15 模块 或 > 8 贡献者）必须做反 Conway 检查：
- 当前代码架构是否反映组织沟通结构？
- 若不反映，可能需要 Inverse Conway（先画架构再倒推组织）

## 已知反例

| 反例 | 教训 |
|---|---|
| Linux 1991-1994 | 一人 → 单点结构（Conway 自然成立） |
| Linux 2018 | 4000 万行 + 单点 → 单点疲惫 |
| CDPR | 8000 页文档散落多平台 → 无统一架构 → 崩盘 |
| Microservices 团队 | "每个服务一队"是显式 Inverse Conway |
| Spotify Model | tribe/squad 结构 = 特定 Inverse Conway 选择 |

## 与本 skill 的关系

- v6 原则 8（健康度挂钩）的 bus factor 指标 = Conway 健康度
- v6 原则 5（规模自适应）的 ≤ 6 上限 = n(n-1)/2 沟通复杂度上限
- references/scaling-modes.md 的"超大项目"判定 = Conway 已失效的阈值

## 警示

- Conway 不是单值因果：相同组织拓扑可产出不同架构
- 不要把 Conway 当成"必须严格执行"——它是观察 + 启发工具
- 反 Conway 检查失败时，可能需要拆分项目（不是加 agent）