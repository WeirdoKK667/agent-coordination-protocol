# Agent Coordination Protocol

> 中大型项目 / 多 agent 协作的协调机制 — 身份分工 + 任务派发 + 认领协议 + 健康度挂钩 + 重议机制

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-0.1.0-blue.svg)](CHANGELOG.md)
[![Claude Code Skill](https://img.shields.io/badge/Claude-Code%20Skill-blueviolet)](https://docs.claude.com)

## 这是什么

给 subagent 发身份牌、划定职责边界、维护 read 最小契约的整套方法。

**默认身份只有 main**（项目总负责人），其他身份按项目需要从候选清单"招聘"——不预设具体岗位清单，跨领域通用。

## 核心特性

- **8 条原则默认全开**：身份是单位 / 占位不养空卡 / 职责隔离 / main 默认 / 规模自适应 / 协议即代码 / 可信度写死+重议 / 健康度挂钩
- **规模自适应**：身份卡 ≤ 6 硬上限（可主动突破）；模块数 ≤ 3 自动进小项目旁路模式
- **协议即代码**：登记表走 PR + review（借鉴 Linux MAINTAINERS + Kubernetes OWNERS）
- **测试可信度硬条款**：一条命令可复现 / PASS-FAIL 计数 / 失败留痕 / 报 commit hash
- **健康度挂钩**：CHAOSS 指标自动算 + 阈值告警（避免重蹈 Linux 2018 / XZ Utils 2024）
- **重议机制**：每 4 周或每 milestone 触发（借鉴 Stripe RFS）

## 适用场景

- 中大型项目开工 / 重组 / 接新模块
- 多 agent 协作需要给 subagent 发身份牌
- 团队组建 / 角色卡 / 团队重组
- 需要协调机制防止"实现测一遍、测试测一遍、main 不信又测一遍"的三方浪费

## 目录结构

```
agent-coordination-protocol/
├── SKILL.md                              # 主流程（双工作流 + 8 原则）
├── templates/                            # 13 个模板
│   ├── identity-card.md                  # 通用 IDENTITY 模板
│   ├── identity-main.md                  # main 预填卡
│   ├── role-recruitment.md               # 招聘决策
│   ├── dispatch-command.md               # 派发 A
│   ├── dispatch-file.md                  # 派发 B
│   ├── placeholder-registry.md           # 占位登记表
│   ├── subtask-list.md                   # 次级任务清单
│   ├── claim-protocol.md                 # 认领协议
│   ├── test-credibility.md               # 可信度条款
│   ├── handoff-checklist.md              # 跨岗交接
│   ├── registry-pr-template.md           # 登记表 PR 模板
│   ├── credibility-revisit-protocol.md   # 重议协议
│   └── health-checklist.md               # 健康度清单
├── references/                           # 13 个参考
│   ├── eight-pinning-principles.md
│   ├── role-catalog.md
│   ├── regular-workflow.md
│   ├── dispatch-formats.md
│   ├── three-layer-structure.md
│   ├── input-collection-checklist.md
│   ├── competitive-landscape.md
│   ├── conway-inverse-maneuver.md
│   ├── scaling-modes.md
│   ├── code-as-registry.md
│   ├── health-metrics.md
│   ├── identity-revocation.md
│   └── redundancy-validation.md
└── examples/
    └── README.md                         # 端到端示例
```

## 快速开始

### 作为 Claude Code skill 使用

1. 拷贝 `agent-coordination-protocol/` 到 `~/.claude/skills/`
2. Claude Code 自动加载，触发条件见 SKILL.md frontmatter description
3. 调用场景：中大型项目开工 / 重组 / 给 subagent 发身份牌时

### 作为项目内的协调机制使用

1. 在项目根执行 `cp -r agent-coordination-protocol/templates/* your-project/roles/`
2. 按 SKILL.md 主流程铺岗卡
3. 走协议即代码流程（PR + review）

## 8 条原则速查

1. **身份是单位**——IDENTITY-*.md 是身份卡不是模块卡
2. **占位岗不养空卡**——只登记不预生成
3. **职责隔离（按需）**——验证类岗必须独立、可信度写死
4. **main 默认**——项目启动即存在
5. **规模自适应**——默认 ≤ 6 卡硬上限，可主动突破
6. **协议即代码**——走 PR 流程
7. **可信度写死+重议**——§四 4 条 + 每 4 周重议
8. **健康度挂钩**——CHAOSS 指标 + 告警响应

详见 [references/eight-pinning-principles.md](references/eight-pinning-principles.md)

## 调研依据

本方法论基于：

- **AI agent 圈层**：anthropics/skills、context-aware-delegation、obra/superpowers 等调研
- **经典项目管理**：PMBOK、Conway's Law（1968）、Scrum、Team Topologies、Shape Up
- **大型工程实践**：Linux MAINTAINERS、Kubernetes OWNERS、Stripe RFS、CHAOSS 指标
- **反向案例**：Linux 2018（Linus 休假）、XZ Utils 2024（后门）、CDPR Crunch、Valve 隐性等级、RACI 膨胀

详见 [references/competitive-landscape.md](references/competitive-landscape.md)

## 版本

- **v0.1.0** (2026-09-02) — 试用版，28 文件

## License

MIT