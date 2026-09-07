# 任务派发机制详解（references/dispatch-formats.md）

> main 怎么把任务转给具体岗位 agent。两种形式 + 一种特殊模式。

## 形式 A：用户复制命令（手派）

**何时用**：
- 用户在不同 IDE / 不同会话里手动操作
- main 与目标 agent 不在同一进程

**怎么用**：
1. main 生成派发材料（必含任务描述 / 目标岗身份卡路径 / read 契约缩小版 / REQ-DESIGN 引用）
2. 用 templates/dispatch-command.md 渲染可粘贴文本
3. 用户复制粘贴到目标 agent 会话

**第一行强制**：
```
身份牌：id-card-<昵称>.md（开工前先读，按 read 最小契约执行）
```

**优**：
- 不依赖文件系统共享
- 用户有最终确认权

**劣**：
- 手动操作易遗漏
- 不适合高频派发

## 形式 B：命令文件（半自动）

**何时用**：
- agent 自己能发现任务（inbox 轮询 / 文件监听）
- main 与目标 agent 共享文件系统

**怎么用**：
1. main 生成派发材料
2. 用 templates/dispatch-file.md 渲染 `dispatch-<id>.md`
3. 落到 `.claude/coordination/active/dispatch-<id>.md`
4. 目标 agent 自动 / 手动读取并启动常规工作流
5. 完成后归档到 `.claude/coordination/archive/`

**优**：
- 可审计（dispatch 文件本身就是证据）
- 可批量处理
- 适合多 agent 协作

**劣**：
- 依赖文件系统共享
- inbox 目录需约定

## 形式 C：派 subagent（同会话直接派）

**何时用**：
- main 用 subagent 工具直接派活（同会话）

**怎么用**：
- 输入：任务描述 + 对应 身份卡 read 契约缩小版
- 输出：结论（不是原文）
- main 只消费结论不重跑

**与 A/B 关系**：
- A/B 是给"目标 agent 不在 main 同一会话"的情况
- C 是同会话直接派，最快但不能跨会话保留状态

## 形式选择依据

| 情况 | 推荐形式 |
|---|---|
| 跨 IDE / 跨会话 / 手动 | A |
| 共享文件系统 / 高频派发 | B |
| 同会话 / 快速派发 | C |
| 关键任务（需要 N 个不同身份并行投票） | C + 真冗余（references/redundancy-validation.md） |

## 派发材料必备字段

无论哪种形式，派发材料必含：
- 任务描述 / 边界 / 交付物
- 目标岗身份卡文件路径
- read 契约缩小版（不是全契约）
- REQ-DESIGN 引用
- 红线清单
- 交工凭证要求
- 下一岗（交接对象）

缺一项派发无效。