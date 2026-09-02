# 招聘决策模板（主工作流用）

> 何时招 / 怎么招 / 招谁 / 招后如何认领。结合 v6 规模自适应（≤ 6 硬上限 + 突破通道）。

## 何时招

满足任一即招：

- 项目出现新职责无人承担（如新增模块、新协议、新工作流）
- 现有岗工作量饱和（健康度告警触发）
- 单 main 决策间隔 > 72h（健康度告警触发分权）
- 用户 / 文档明确要求新分工

## 怎么招

```
1. 读 references/eight-pinning-principles.md 确认不破坏原则
2. 读 references/input-collection-checklist.md 采集项目输入
3. 读 references/role-catalog.md 从抽象类别选岗
4. 数量检查：累计（含 main）≤ 6（默认硬上限）
   - 若超过：
     a. main 可主动调整上限（理由写 PR 描述）
     b. 或用户调用 skill 时特别注明不硬
     c. 否则必须合并或拒绝招新
5. 用 templates/identity-card.md 渲染 IDENTITY-<name>.md
6. 若涉及验证/测试，把 templates/test-credibility.md 原样嵌入
7. 在登记表加条目（走 PR，templates/registry-pr-template.md）
8. 设重议日（每 4 周）
10. 健康度挂钩更新（templates/health-checklist.md）
```

## 招谁（参考 role-catalog.md）

抽象类别（按需具名）：

- 实现类 / 验证类 / 设计类 / 数据·内容类 / 架构类 / 运维·集成类 / 感官·呈现类 / 协调·管理类

每个类别附：
- 典型职责范围
- IDENTITY 字段提示
- 是否触发职责隔离原则（仅验证类）
- 交叉引用「上游岗：IDENTITY-main.md」

## 招后如何认领

- IDENTITY-<name>.md 顶部加交叉引用行"上游岗：IDENTITY-main.md"
- 在占位登记表把状态从 [空] 改 [招]
- 走 GitHub PR + reviewer approve（templates/registry-pr-template.md）
- 接替者必须 ≥ 2（v6 bus factor）——若 < 2 触发招人或拆分

---

## 不要做的事

- 不要无理由招新——每个新岗必须有明确职责边界
- 不要让单个岗的工作量超过 main（main 是 fallback）
- 不要超过 6 硬上限——除非走突破通道