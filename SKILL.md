---
name: agent-coordination-protocol
version: 0.1.0
description: |
  中大型项目 / 多 agent 协作场景下，需要给 subagent 发身份牌 / 划定职责边界 /
  维护 read 最小契约 / 团队组建 / 角色卡 / 团队重组 / 协调多 agent 时使用。
  本 skill 默认身份只有 main，其他身份按项目需要从 references/role-catalog.md
  候选清单里"招聘"——不预设具体岗位清单，跨领域通用。整套协调机制覆盖：
  占位登记表 / 次级任务清单 / 认领协议 / 测试可信度条款 / 健康度挂钩 / 重议
  机制。默认配置偏稳定：身份卡 ≤ 6 硬上限（可主动突破）、模块数 ≤ 3 进小项目
  旁路模式、协议走 PR、每 4 周重议、自动算 bus factor 与 main 负载告警。
  身份是单位、模块降级为职责内容、占位岗不养空卡、验证类岗位必须独立可信度
  写死。
trigger_words:
  - 中大型项目
  - 多 agent 协作
  - 协调 / coordination
  - subagent 身份牌
  - 团队组建
  - 角色卡
  - 职责边界
  - read 最小契约
  - 岗位（身份）
---

# Role Assignment Skill v0.1

> 0.1 试用版。输入项目概况，按 8 条原则自动派生岗位卡体系 + 健康度挂钩。
> 默认配置偏稳定——所有约束默认开启，需要主动 / 用户注明才能关闭。

---

## 0. 八条不可破坏原则（默认全开）

1. **身份是单位、模块降级为职责内容**（§一）—— IDENTITY-*.md 是身份卡不是模块卡
2. **占位岗不养空卡**（§一）—— 只产出占位登记表，不预生成具体占位 IDENTITY
3. **职责隔离原则（按需触发）**（§一）—— 招验证类岗必须独立、可信度写死、不可由实现类兼任
4. **main 是默认身份，不是招出来的**（v5）—— IDENTITY-main.md 由本 skill 预填，项目启动即存在
5. **规模自适应**（v6）—— 默认 ≤ 6 卡硬上限；模块数 ≤ 3 + 贡献者数 ≤ 2 自动进小项目旁路模式
   - **突破通道**：main 招聘决策可主动调整上限（理由写 PR）；用户调用时特别注明特定需求不硬
   - **留痕**：所有突破 ≤ 6 的决策必须写明理由，不可静默
6. **协议即代码**（v6）—— 登记表 / 认领协议 / 可信度条款与项目代码同仓，走 GitHub PR + review
7. **可信度写死 + 强制重议**（v6）—— §四 4 条 + 每 4 周/里程碑重议评审（Stripe RFS），可修改不可删
8. **健康度挂钩**（v6）—— 自动算 CHAOSS 指标（bus factor / main 负载 / 贡献者变更率 / issue 解决时间），触发阈值自动告警——告警必须响应

**可选机制**（默认关闭）：⑨ 真冗余验证（N 身份并行投票，警示多 LLM 17× 错误率）；⑩ 元层反思（周期性问"LLM 是否真有自治"）。

读 `references/eight-pinning-principles.md` 看速查表。

---

## 1. 主工作流：招聘 + 派发（main 视角）

```
0. 加载本 skill → 命中 trigger words → 确认场景
1. 规模检测：读 references/scaling-modes.md
   - 输入：模块数 / 贡献者数 / 代码量 / 现有协议复杂度
   - 输出：规模档位（小作坊 / 中型 / 大型 / 超大）
2. 默认身份 main：先落 IDENTITY-main.md（用 templates/identity-main.md 预填）
3. 规模档位决定配置：
   - 小作坊（≤ 3 模块 + ≤ 2 贡献者）→ 小项目旁路模式
     * 只发 IDENTITY-main.md
     * 占位登记表 / 任务清单 / 协议走简化模板
     * 跳过可信度写死 + 健康度挂钩（main 自己验）
   - 中型 / 大型 → 走完整流程（步骤 4-）
   - 超大（> 15 模块 或 > 8 贡献者）→ 强制分权警告
4. 招聘流程：
   4.1 读 references/eight-pinning-principles.md
   4.2 读 references/input-collection-checklist.md，按 checklist 采集项目输入
   4.3 读 references/role-catalog.md，从抽象岗位类别选岗（或自定义）
   4.4 数量硬约束：累计（含 main）默认 ≤ 6 硬上限
        - main 可主动调整上限（理由写 PR）；用户特别注明不硬
   4.5 对每个招的岗：
        - 用 templates/identity-card.md 渲染 IDENTITY-<岗>.md
        - 若涉及验证/测试，把 templates/test-credibility.md 原样嵌入
        - 顶部加"上游岗：IDENTITY-main.md"交叉引用
   4.6 用 templates/placeholder-registry.md 登记（走 PR 流程，templates/registry-pr-template.md）
   4.7 用 templates/subtask-list.md 登记小型 routine 工作流
   4.8 用 templates/claim-protocol.md 写入认领协议段
   4.9 用 templates/health-checklist.md 初始化 CHAOSS 健康度挂钩
5. 输出形态判断：
   - 项目根含 .claude/agents/ → 按官方 subagent frontmatter 语法输出（兼容加载）
   - 否则按纯 markdown 输出
6. 八原则自检 + 落盘到项目根 roles/
```

—— main 自带任务 / 用户提新需求时 ——

```
7. 任务派发（main 拿到新任务）：
   7.1 读各岗 IDENTITY 卡的"职责清单"匹配任务能力
   7.2 读匹配岗的"read 最小契约"取缩小版
   7.3 生成派发材料（必含任务描述 / 目标岗 IDENTITY 路径 / read 契约缩小版 / REQ-DESIGN 引用）
   7.4 强制行："身份牌：IDENTITY-<岗>.md（开工前先读，按 read 最小契约执行）"
   7.5 选派发形式（读 references/dispatch-formats.md）：
        - 形式 A 用户复制命令（templates/dispatch-command.md）
        - 形式 B 命令文件落到 roles/inbox/（templates/dispatch-file.md）
   7.6 main 等目标岗走常规工作流；结论（不是原文）回流 main
```

—— 周期触发 ——

```
8. 重议周期触发（每 4 周或每 milestone）：
   - 读 templates/credibility-revisit-protocol.md
   - main 召集、相关 agent 投票、可修改可信度条款但不可删
9. 健康度告警响应（CHAOSS 仪表盘）：
   - 告警响应方式：临时挂起 / 触发分权 / 招新人 / 调用重议
   - 不绕过健康度告警——告警必须响应
```

---

## 2. 常规工作流：领工（任意 agent 视角）

```
0. 接到任务通知（形式 A 用户粘贴 / 形式 B 读 roles/inbox/）
1. agent 启动：
   - 开工自检：git fetch + status 干净 + 无 ahead
   - 读 IDENTITY-<本岗>.md（领身份）
   - 读 references/regular-workflow.md（确认本岗完整流程）
   - 读 references/health-metrics.md 确认自己当前负载
2. 拿任务：读 REQ-<id>.md / DESIGN-<id>.md，确认边界/交付物/验收标准
3. 明确工作内容 / 范围 / 交接点：
   - read 最小契约执行（必读 vs 显式无需读）
   - 红线对照清单
4. 干活
5. 交工凭证：git push + 实跑输出 + commit hash + REQ 单回填 + 红线自检
6. 交接：用 templates/handoff-checklist.md 交下一岗，材料：变更清单 + 测试输出 + commit hash
```

---

## 3. 派 subagent 模式（main 同会话直接派活）

```
- 输入：任务描述 + 对应 IDENTITY 卡 read 契约缩小版
- 输出：结论（不是原文）—— pass/fail + 关键证据
- main 只消费结论不重跑（§三 末 认领协议硬条款）
- subagent 走完整 §2 流程，read 范围被缩小
- 与形式 A/B 关系：A/B 是给"目标 agent 不在 main 同一会话"的情况；本模式是同会话直接派
- 可选真冗余（v6）：关键任务启用 N 个不同身份并行投票，读 references/redundancy-validation.md
```

---

## 4. 文件加载指引（按需读）

| 何时读 | 读哪个文件 |
|---|---|
| 8 条原则速查 | references/eight-pinning-principles.md |
| 招什么岗 | references/role-catalog.md |
| 输入采集 | references/input-collection-checklist.md |
| 常规工作流步骤 | references/regular-workflow.md |
| 派发形式选择 | references/dispatch-formats.md |
| 规模档位判定 | references/scaling-modes.md |
| 协议走 PR 流程 | references/code-as-registry.md |
| CHAOSS 健康度挂钩 | references/health-metrics.md |
| 可撤销身份机制 | references/identity-revocation.md |
| 真冗余验证 | references/redundancy-validation.md |
| 反向 Conway 教学 | references/conway-inverse-maneuver.md |
| 三层结构 | references/three-layer-structure.md |
| 竞品对比 | references/competitive-landscape.md |

---

## 5. 不做的事

- 不预设具体岗位清单（实现/测试/内容/数值/...）—— 交给 main 按项目需要招聘
- 不绑定任何领域（游戏/Web/移动/数据/...）—— 交给 main 用 role-catalog.md 选用
- 不预生成具体项目的 IDENTITY-*.md 内容（main 拿到模板后自己填）
- 不解决岗位冲突（同文件两个岗要主所有权时由 main 决策）
- 不在 SKILL.md 主体硬写案例（案例只在 examples/README.md 一个文件里匿名化呈现）
- 不绕过健康度告警（告警必须响应）
- 不写完协议就结束（必须初始化健康度挂钩 + 设重议周期）
- 不无上限加身份——默认 ≤ 6 硬上限，但允许 main 主动调整 / 用户特殊需求突破（带理由留痕，不静默）

---

## 6. 版本

v0.1（试用版，2026-09-02）：完整 8 条原则 + 30 文件落盘。