# Harness Design Patterns for Skills

用于设计多 agent、高风险工具、MCP、长流程自动化类 skill。普通简单 skill 不必套用。

---

## 核心原则

**Agent 负责局部智能，Skill 负责全局约束。**

Agent 可以负责判断、生成、总结、局部决策；Skill 必须定义生命周期、边界、工具权限、验证、预算、终止条件和审计路径。

---

## 适用信号

出现任一信号，应考虑创建 `harness.md`：

- 多个 agent 协作
- 后台监控或并行任务
- MCP Server / 外部工具接入
- 文件写入、删除、代码执行、数据库写、外部系统写
- 长流程、多阶段、可重试
- 需要 trace、审计、回放
- 需要控制 token、工具调用次数、执行时长

---

## 任务生命周期模板

```md
## Task Lifecycle

1. **CREATE** — 收集任务输入，确定目标和边界。
2. **PLAN** — 生成声明式计划，不直接执行工具。
3. **AUTHORIZE** — 检查工具权限、风险等级和是否需要用户确认。
4. **EXECUTE** — 按计划执行，每步写入 execution log。
5. **VERIFY** — 验证最终结果和执行轨迹。
6. **COMPLETE / FAIL** — 输出结果或明确失败原因。
```

### 好

Planner 输出声明式计划：

```json
{"step": 1, "intent": "inspect", "tool": "Read", "input": "...", "risk": "low"}
```

Skill/Harness 决定是否执行。

### 坏

Planner 直接决定并调用高风险工具，skill 没有中间审查点。

---

## 工具治理模板

```md
## Tool Registry

| Tool | Purpose | Allowed Agent | Risk | Needs Confirmation | Output | Audit |
|------|---------|---------------|------|--------------------|--------|-------|
| Read | 读取本地文件 | main/explore | low | no | file content | log path |
| Edit | 修改文件 | main | medium | no for local reversible | diff | log diff |
| Bash deploy | 远程部署 | main | high | depends on env | command output | full trace |
```

**规则**：
- 工具不是函数调用，而是授权点。
- 高风险工具必须进 ASK/BLOCK 决策表。
- MCP Server 不能裸奔给 agent，必须经过白名单和风险分级。

---

## 预算与硬终止模板

```md
## Budget & Stop Conditions

- max_steps: {{N}}
- max_tool_calls: {{N}}
- max_duration: {{duration}}
- max_retries_per_step: {{N}}

### Degradation

| 区间 | 动作 |
|------|------|
| 绿区 | 正常执行 |
| 黄区 | 压缩上下文，减少非必要工具调用 |
| 红区 | 收束范围，只保留关键验证 |
| 熔断 | 停止执行，返回 partial result 和阻塞点 |
```

---

## 状态与记忆分层

| 类型 | 生命周期 | 用途 | 写入位置 |
|------|----------|------|----------|
| Working State | 当前步骤 | 临时上下文 | 不写 memory |
| Session State | 当前会话 | 跨步骤共享 | 任务/会话上下文 |
| Execution Log | 长期/审计 | 回放、评估、追责 | log / report |
| Episodic Memory | 长期 | 用户偏好、踩坑经验 | memory |
| Semantic Memory | 长期 | 业务规则、工具约束 | docs / skill |

**规则**：
- 临时状态不进长期 memory。
- 事实型/合规型任务必须保留原始引用。
- 过期 memory 要能清理，不让记忆只增不删。

---

## 轨迹评估模板

不要只问“结果对不对”，还要问：

- 工具是否选对？
- 参数是否合规？
- 是否重复调用？
- 是否无意义重试？
- 是否调用了未授权工具？
- 是否因为上下文压缩丢了关键约束？
- 是否达到用户目标？
- 是否有可回放 trace？

确定性优先：单元测试、schema 校验、命令结果、规则检查。LLM-as-Judge 只用于开放式表达质量。

---

## 落地阶段

| 阶段 | 目标 | 不做什么 |
|------|------|----------|
| MVP | 跑通一条端到端闭环 | 不上复杂动态 planner / 大量 agent |
| Hardening | 增加预算、权限、重试、压缩、轨迹评估、审计 | 不扩场景 |
| Scale | 多场景、多租户、模型路由、A/B、成本看板 | 不牺牲治理边界 |
```
