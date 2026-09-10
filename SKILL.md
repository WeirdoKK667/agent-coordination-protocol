---
name: agent-coordination-protocol
description: 多 agent / 多终端协作场景的协调协议——给 subagent 发身份牌（id-card / role）、派发任务（dispatch）+ 交接（handoff）、复用预设岗位（main-designer / implementer / verifier / artist）、团队组建时使用。当用户提到"派活给另一个 agent""多 agent 协作""subagent 身份""分工""团队""handoff""dispatch""派发""交接"时加载。
---

# Agent Coordination Protocol

多 agent 协作的最小集：**身份 + 派发 + 交接**。

---

## 流程（5 步）

任何 agent（main / implementer / verifier / artist）工作都按这 5 步：

```
1. 接身份    → 读 roles/<role>.md
2. 接任务    → 读 dispatch 文件
3. 明确权限  → 必读 / 显式无需读 / 可改 / 不可改
4. 完成任务  → 按身份职责 + 任务边界执行
5. 完成 report → 落 handoff 文件 + 改 status + push
```

---

## 身份（预设岗位，直接调用）

| 岗位 | 文件 | 一句话 |
|------|------|--------|
| main-designer | `roles/main-designer.md` | 主设计 / 唯一对外接口 / 派发 + 验收 |
| implementer | `roles/implementer.md` | 默认实现者 |
| verifier | `roles/verifier.md` | 独立验证岗（与 implementer 不可兼任）|
| artist | `roles/artist.md` | 创作者（资产 / 视觉 / 调研）|

新岗位用 `roles/_template.md` 派生。

---

## 文档模板

| 文档 | 文件 | 何时用 |
|------|------|--------|
| 派发单 | `templates/dispatch.md` | main-designer 给 subagent 派活 |
| 交接单 | `templates/handoff.md` | 任何 agent 完成时落 |

---

## 原则（6 条）

1. **身份是单位**——按岗招人，不按模块设卡。
2. **预设可直接调**——常用岗位已写好，新岗从模板派生。
3. **验证岗独立**——verifier 与 implementer 不可兼任。
4. **main-designer 默认**——项目启动即存在，不需招聘。
5. **协议即代码**——协议与项目同仓，走 git。
6. **可改不可删**——可信度条款可调整，不可删除。

---

## 状态机

```
open ──→ claimed ──→ in_progress ──→ handoff ──→ done
         (imp 接单)  (imp 干活)     (imp 交工)  (main 验收)
```

**关键约束**：改 status `in_progress → handoff` 之前，必须先落 handoff 文件。缺 handoff 文件的 status 变更视为无效。

---

## 做什么

1. 接身份 → 接任务 → 干活 → 落 handoff
2. 遇设计矛盾 → 写 handoff §疑问，不擅自处理
3. 遇范围外问题 → 写 handoff §deferred，不顺手修

---

## 不做什么

- 不擅自解红线
- 不在 handoff 落盘前改 status = handoff
- 不重跑验证岗结论
- 不顺手修范围外 bug
- 不在 SKILL.md 里堆实战细节

---

## 文件清单

```
SKILL.md                  本文件（流程 + 原则）
roles/                    预设岗位（直接调用）
  main-designer.md
  implementer.md
  verifier.md
  artist.md
  _template.md            派生新岗位用
templates/                文档模板
  dispatch.md
  handoff.md
```

---

## 版本

- **v3.0**：整体重写，删实战细节 / 验证状态噪声；岗位预设独立成 `roles/`。
- **v0.2.1**：handoff 强前置约束（保留）。
