# 次级任务清单 schema（不预设具体任务）

> 次级任务 = 项目特定的小型 routine 工作流（"注册新 X" / "绘制 Y 图" / ...）。不单独发身份牌，派 subagent 干活时临时指定身份。

## Schema

```markdown
# 次级任务清单

> 维护人：IDENTITY-main.md  
> 协议：见 templates/registry-pr-template.md（每次变更走 PR）

## 任务条目

### <任务名 1>

- **触发条件**：<什么情况下激活>
- **check 清单**：
  - [ ] <步骤 1>
  - [ ] <步骤 2>
  - [ ] <步骤 3>
- **默认派发方式**：<subagent 临时指定身份 / 形式 A / 形式 B>
- **关联 IDENTITY**：<临时身份对应的 IDENTITY 卡，如无则用 main 兼任>
- **关联产物**：<落盘到哪>

### <任务名 2>
（同上）

### <任务名 3>
（同上）
```

## 渲染示例

```markdown
# 次级任务清单

> 维护人：IDENTITY-main.md

## 任务条目

### 绘制架构图

- **触发条件**：新模块接入 / 重构 / 季度架构审视
- **check 清单**：
  - [ ] 读 references/three-layer-structure.md
  - [ ] 读 references/conway-inverse-maneuver.md
  - [ ] 画架构图（模块边界 + 数据流 + 共享文件权限）
  - [ ] 落 `docs/architecture/<date>.md`
- **默认派发方式**：形式 B 命令文件（dispatch-architecture-<date>.md）
- **关联 IDENTITY**：架构类岗（如无，main 兼任）

### 注册新数据条目

- **触发条件**：有新数据需要加入 data/registry/
- **check 清单**：
  - [ ] 读 IDENTITY 数据/内容类岗
  - [ ] 校验 schema
  - [ ] 写入 data/registry/<条目>.yaml
  - [ ] 走 PR 走 review
- **默认派发方式**：subagent 临时身份（数据/内容类）
- **关联 IDENTITY**：IDENTITY-数据内容.md（如招出）
```

---

## 不要做的事

- 不要把"实现某个模块"这种大型工作当次级任务——次级任务是 routine 流水线
- 不要让次级任务绕过认领协议——必须附 IDENTITY 卡或临时身份
- 不要让次级任务清单无限膨胀——定期清理已废弃任务