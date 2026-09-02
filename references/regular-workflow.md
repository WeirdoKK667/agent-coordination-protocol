# 常规工作流（领工，agent 视角）

> 每个被招出的 agent 都要走一遍。从接到任务到交工的完整动作序列。

## 步骤 0：接到任务通知

- 形式 A：用户把粘贴的命令喂给 agent 会话（templates/dispatch-command.md）
- 形式 B：agent 自己读 `roles/inbox/dispatch-<id>.md`（templates/dispatch-file.md）

## 步骤 1：agent 启动

```
1. 开工自检命令：
   git fetch && git status --porcelain && git log @{u}.. --oneline
   （应为空输出，否则停，先解决）

2. 读 IDENTITY-<本岗>.md（领身份）
3. 读 references/eight-pinning-principles.md（确认不破坏原则）
4. 读 references/health-metrics.md 确认自己当前负载
```

## 步骤 2：拿任务

```
1. 读 REQ-<id>.md / DESIGN-<id>.md
2. 确认任务边界 / 交付物 / 验收标准
3. 若有歧义，停下来找 main，不要猜
```

## 步骤 3：明确工作内容 / 范围 / 交接点

```
1. read 最小契约执行：
   - 必读：IDENTITY 卡 + REQ 单 + 本岗设计稿相关节 + 改动函数 + 断言对应测试段
   - 显式无需读：归档 / 其他岗设计稿 / 视图层 / legacy
2. 红线对照清单：
   - 永久不碰的文件
   - 共享文件权限边界
3. 确认下一岗（交接对象）
```

## 步骤 4：干活

```
- 按 IDENTITY 卡里"职责清单"执行
- 任何越界动作停下来找 main
- 不绕过健康度告警
```

## 步骤 5：交工凭证

```
1. git push（先 push 再交工）
2. 实跑输出：
   - PASS/FAIL 计数
   - 失败日志落 <project>/logs/<job>-<date>.log（含用例名 + 原因 + commit hash）
3. 报远程 commit hash + remote branch
4. 需求单回填：REQ 单写"已完成 + commit hash + 验收岗"
5. 红线对照自检：确认没越界
```

## 步骤 6：交接

```
1. 按 IDENTITY 卡"交接点"清单交给下一岗
2. 用 templates/handoff-checklist.md 走交接
3. 交接材料：变更清单 + 测试输出 + commit hash
4. main 收到验收结论（不重跑，只看）
```

## 关键红线

- **不重跑 main 的验收**：除非 §四 可信度被破
- **不省 read 最小契约**：不读必读项 = 越界风险
- **不绕过健康度告警**：告警必响应
- **不省交工凭证**：缺一项即交接无效

## 出错时

- 开工自检失败 → 找 main
- 任务边界不清 → 找 main
- 越界动作 → 立即停 + 找 main
- 健康度告警 → 必响应，不可静默忽略
- 验证失败 → 修复后重新走步骤 5