# Changelog

## 3.0 (2026-09-10)

整体重写。按用户反馈：

- **删**：所有"验证/未验证"实战状态描述
- **删**：20 单实战细节（commit 锚点时序 / 串行接单 / 测试员模式 A/B/C / 探针单 / session 接续点 / 中断恢复等）
- **删**：9 个 references + 14 个多余 templates + examples/
- **改**：工作流简化为 5 步抽象（接身份 → 接任务 → 明确权限 → 完成任务 → 完成 report）
- **改**：原则简化为 6 条（去繁就简）
- **改**：岗位预设独立成 `roles/` 目录（main-designer / implementer / verifier / artist / _template）
- **改**：templates 精简到 2 文件（dispatch.md + handoff.md）

**保留**：
- handoff 强前置约束（v0.2.1 唯一产出）
- 6 原则（精简版）
- 状态机 4 态（open → claimed → in_progress → handoff → done）

**文件清单（v3.0）**：
```
SKILL.md           主流程 + 原则
README.md          入口说明
CHANGELOG.md
LICENSE
roles/             5 文件
templates/         2 文件
合计 11 文件（v0.2.1 = 36 文件，-69%）
```

---

## 0.2.1 (2026-09-10)

handoff 落盘强前置约束（dispatch-2026-09-09-007 RED 修复）。

- `SKILL.md` §2.5 新增：handoff 落盘 8 步强顺序
- `templates/dispatch-file.md` §交工凭证：handoff → status → push 强顺序
- `templates/handoff-checklist.md` 交接材料第一项 = handoff 已落盘
- `references/regular-workflow.md` 步骤 5：8 步强顺序 + 前置条件
- `templates/handoff-template.md`（原 report-template.md 重命名）
- `references/lessons-learned.md`（RED 现场记录）

## 0.2.0 (2026-09-08)

首次实战反馈整合（20 单闭环）。

- frontmatter 修正（删 `version` / `trigger_words` 非官方字段）
- 结构对齐：`roles/inbox/` → `.claude/coordination/active/`；`IDENTITY-` → `id-card-`
- 新增机制：commit 锚点时序 / 串行接单 / 测试员 A/B/C / 探针单 / session 接续点 / 中断恢复三分类 / 反向纠错 37% 数据
- 新增模板：`identity-verifier.md`、`resume-point.md`
- handoff 补 §测试员耗时 段（修 v0.1 自指断裂）

## 0.1.0 (2026-09-02)

试用版首推。8 条原则 + 30 文件落盘（未经实战）。
