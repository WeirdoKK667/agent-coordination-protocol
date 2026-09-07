# examples/README.md

> 极简端到端示例（匿名化，不绑定领域）。展示本 skill 怎么用，不展示具体项目设计。

## 这是什么

本目录放**匿名化示例**，帮 main 第一次用本 skill 时看到端到端用法。

## 这是什么**不是**

- 不是"标准答案"——具体项目设计由 main 校准
- 不是"模板直接抄"——每个项目有自己的协议
- 不是"完整可运行代码"——示例只展示流程骨架

## 示例分类

### 例 1：单岗小作坊（≤ 3 模块 + ≤ 2 贡献者）

**场景**：
- 项目：纯文档项目（README + 几个 markdown 指南）
- 模块：1 个（文档）
- 贡献者：1 个（你自己）

**走的路径**：
1. 加载 skill
2. 规模检测 → 小作坊
3. 落 id-card-main-designer.md（用 templates/identity-main.md 预填）
4. 跳过招聘、跳过健康度挂钩
5. 完成

**产物**：
- `.claude/coordination/id-card-main-designer.md`
- 无登记表 / 无协议（旁路模式）

### 例 2：中型项目（4-10 模块 + 3-8 贡献者）

**场景**：
- 项目：通用 Web 应用
- 模块：5 个（auth / api / db / ui / deploy）
- 贡献者：4 个（你 + 3 个 subagent）

**走的路径**：
1. 加载 skill
2. 规模检测 → 中型
3. 落 id-card-main-designer.md
4. 招 4 个岗：
   - main（已存在）
   - 实现类（id-card-impl-module-auth.md）
   - 实现类（id-card-impl-module-api.md）
   - 验证类（id-card-verifier.md）
5. 写登记表 + 任务清单 + 协议段
6. 初始化健康度挂钩
7. 设置重议日

**产物**：
- `.claude/coordination/id-card-*.md`（5 张）
- `.claude/coordination/registry/placeholder-registry.md`
- `.claude/coordination/subtask-list.md`
- `.claude/coordination/CONVENTIONS.md`（含认领协议）
- `.claude/coordination/health/baseline.md`

### 例 3：关键任务真冗余（可选模式启用）

**场景**：
- 项目：通用 Web 应用（中型）
- 任务：上线 v2 版本

**走的路径**：
1. 招 3 个不同身份 subagent 并行验证：
   - 验证岗跑 §四 四条
   - 架构岗做兼容性检查
   - 数据岗做数据迁移验证
2. 收集 3 份结论
3. main 投票决定是否上线

**产物**：
- `.claude/coordination/decisions/launch-v2-<date>.md`（含 3 份结论 + 投票记录）

---

## 看示例时的注意事项

- 看完后**立即忘掉具体内容**，回到自己的项目结构
- 不要把示例的"角色名"当成"应该用的角色名"
- 用 role-catalog.md 的抽象类别自己选
- 示例文件**只展示流程骨架**，具体 身份卡内容由 main 填

## 后续

确认能跑通后，落盘到自己的项目根 `.claude/coordination/` 目录即可。