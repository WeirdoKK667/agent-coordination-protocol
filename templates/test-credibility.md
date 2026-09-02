# 测试可信度标准段（§四 四条 + v6 重议机制）

> 招出验证/测试类岗时，原样嵌入 IDENTITY-<验证岗>.md。v6 强制重议机制配套。

## §四 四条硬条款（来自参考文档）

### 1. 一条命令可复现

验收命令必须可复制粘贴直接跑，包含：
- 工作目录（cd 命令或相对路径说明）
- 完整参数
- 必要的 `--` 分隔符（**血泪坑**：缺 `--` 分隔符，参数进不了 user args，进程空转永不退出）

```bash
# 示例：godot 跑 autotest（带 -- 分隔符）
cd <project-root>
godot --headless --path . -- --autotest
```

### 2. 输出带计数

验收输出必须含 PASS/FAIL 计数 + 通过判据：

```
Total: 81 | Passed: 81 | Failed: 0
```

### 3. 失败留痕

失败日志必须落盘，可追溯：
- 日志路径（如 `<project>/logs/autotest-<date>.log`）
- 失败用例名 + 失败原因
- 相关 commit hash

### 4. 报 commit hash

验收对象对应 commit，验收**只认远程**（本地 commit 无效）：
- 验收结论必须含：`commit = <hash>` + `branch = <remote-branch>`
- main 看结论不重跑

---

## v6 强制重议机制（Stripe RFS）

### 触发条件

- 每 4 周 或 每 milestone 触发一次
- 触发方式：main 召集，按 templates/credibility-revisit-protocol.md 走

### 重议范围

可修改：
- 通过判据（如 PASS 阈值从 100% 调到 95%）
- 命令参数（如加新测试场景）
- 失败容忍策略

不可修改：
- 必须可复现（一条命令 + 工作目录 + 完整参数 + `--` 分隔符）
- 必须带 PASS/FAIL 计数
- 必须失败留痕
- 必须报远程 commit hash

### 留痕要求

每次重议结论必须写入 `roles/decisions/credibility-revisit-<date>.md`：
- 召集人 / 投票人
- 修改项 + 修改前/后对比
- 下次重议日期

---

## 嵌入 IDENTITY 验证岗卡的示例

```markdown
## 职责清单（验证类岗专属）

### §四 四条硬条款

1. **一条命令可复现**：验收命令见 `<roles/commands/verify.sh>`（含 cd + 完整参数 + `--` 分隔符）
2. **输出带计数**：实跑输出含 `Total: X | Passed: Y | Failed: Z`
3. **失败留痕**：失败日志落 `<project>/logs/verify-<date>.log`，含用例名 + 原因 + commit hash
4. **报 commit hash**：验收结论必须含 `commit = <hash>` + `branch = <remote>`

### §四 重议条款

- 触发：每 4 周 / 每 milestone
- 流程：templates/credibility-revisit-protocol.md
- 不可修改：必须可复现 / 必须带计数 / 必须留痕 / 必须报远程 hash
- 可修改：通过阈值 / 命令参数 / 失败容忍
- 留痕：roles/decisions/credibility-revisit-<date>.md
```

---

## 不要做的事

- 不要把"测试通过"当成交工凭证——必须带 commit hash + 远程 branch
- 不要省 `--` 分隔符——这是血泪教训，进程空转永不退出
- 不要让重议机制被绕过——每 4 周必须触发，不可跳过
- 不要在重议时删除硬条款——只能修改可修改项