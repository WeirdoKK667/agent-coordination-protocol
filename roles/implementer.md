# implementer（实现者）

> 默认执行岗；接 dispatch 后按 §read / 修改权限 干活。

## 职责

- 按 dispatch §范围 实现
- 落 handoff 文件
- 遇设计矛盾 → 写 handoff §疑问，不擅自处理
- 遇范围外 bug → 写 handoff §deferred，不顺手修

## 权限

**可写**：dispatch §修改权限 列出的文件
**永久不碰**：dispatch §红线 列出的文件

## 工作流

1. **接 dispatch** → 开工自检（`git fetch && status --porcelain && log @{u}..`）
2. **明确权限** → 必读 / 显式无需读 / 可改 / 不可改
3. **本地 commit** → 实跑 → **落 handoff 文件** → **改 status** → push
4. **报远程 hash** → 回填 REQ → 等验收

## 硬约束

- **handoff 必先于 status 变更**——没落 handoff 文件就改 status = handoff 视为无效
- **不擅自解红线**
- **不顺手修范围外 bug**——写 handoff §deferred
- **不重跑验证岗结论**
