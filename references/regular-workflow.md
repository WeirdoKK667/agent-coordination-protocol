# 常规工作流（领工，agent 视角）

> 每个被招出的 agent 都要走一遍。从接到任务到交工的完整动作序列。

## 步骤 0：接到任务通知

- 形式 A：用户把粘贴的命令喂给 agent 会话（templates/dispatch-command.md）
- 形式 B：agent 自己读 `.claude/coordination/active/dispatch-<id>.md`（templates/dispatch-file.md）

## 步骤 1：agent 启动

```
1. 开工自检命令：
   git fetch && git status --porcelain && git log @{u}.. --oneline
   （应为空输出，否则停，先解决）

2. 读 id-card-<本岗昵称>.md（领身份）
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
   - 必读：身份卡 + REQ 单 + 本岗设计稿相关节 + 改动函数 + 断言对应测试段
   - 显式无需读：归档 / 其他岗设计稿 / 视图层 / legacy
2. 红线对照清单：
   - 永久不碰的文件
   - 共享文件权限边界
3. 确认下一岗（交接对象）
```

## 步骤 4：干活

```
- 按 身份卡里"职责清单"执行
- 任何越界动作停下来找 main
- 不绕过健康度告警
```

## 步骤 5：交工凭证（强顺序，缺一即无效）

> **铁律**：imp 改 dispatch status: `claimed → done` 的**强前置条件** = report 文件已落盘。
> 没有 report 落盘的 status 变更视为无效，main 不会验收。

```
1. 本地 commit（不 push；commit message 引用 dispatch §复核点）
2. 实跑输出：
   - PASS/FAIL 计数
   - 失败日志落 <project>/logs/<job>-<date>.log（含用例名 + 原因 + commit hash）
3. 落 report 文件（必做）：inbox/report-<YYYY-MM-DD>-<NNN>.md
   - 模板见 templates/report-template.md
   - 含 §完成总结 / §归档建议 / §新发现问题 / §下次 session 接续点 4 段
   - 缺此文件 → 后续 status 变更视为无效
4. 改 dispatch status: claimed → done（回填交工 commit hash）
   - 前置条件：步骤 3 已完成
5. git push（report 落盘 + status 改完后再 push）
6. 报远程 commit hash + remote branch
7. 需求单回填：REQ 单写"已完成 + commit hash + 验收岗"
8. 红线对照自检：确认没越界
```

## 步骤 6：交接

```
1. 按 身份卡"交接点"清单交给下一岗
2. 用 templates/handoff-checklist.md 走交接
   - 交接材料第一项 = "report 文件已落盘"（必做）
3. 交接材料：report + 变更清单 + 测试输出 + commit hash
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