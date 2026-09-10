# Dispatch: <任务名>

> **派发**：<from-role> → **收单**：<to-role>
> **状态**：open → claimed → in_progress → handoff

## 身份牌

`<role>.md`（开工前先读）

## 任务

<一段话讲清目标 + 边界 + 交付物>

## Read 范围

**必读**：
- <文件 1>
- <文件 2>

**显式无需读**：
- <其他设计稿 / 归档 / 视图层>

## 修改权限

**可改**：<文件列表>
**不可改（红线）**：<红线文件列表>

## 验收标准

1. <可验证项 1>
2. <可验证项 2>

## 红线

- <不可触的文件 / 不可调的 API>

## 交接

完成后落 handoff（`templates/handoff.md`）→ 改 status → push → 报 hash。
