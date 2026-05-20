# Skill Design Patterns

收录 write-a-skill 学习到的好/坏样板。用于创建、审计、教学 skill 时按需读取。

---

## Description 样板

### 好

```yaml
description: 设计、创建并审计 Claude Code Agent Skill。Use when: 用户想创建新 skill、优化已有 skill、或说“分析这个 skill”。Do NOT use for: 仅讨论理念但无实际交付意图。
```

**好在哪里**：
- capability 明确
- Use when 有具体触发词
- Do NOT use for 明确排除边界

### 坏

```yaml
description: Helps with skills.
```

**坏在哪里**：
- 不知道何时触发
- 无法和其他 agent/skill 区分
- 没有排除场景

---

## Phase 样板

### 好

```md
## Phase 2：复杂度判断

**目标**：判断 SIMPLE / COMPLEX。

**操作**：
- 读取用户描述
- 匹配复杂度信号
- 记录判断依据

**完成标准**：确定 SIMPLE 或 COMPLEX，无歧义。
```

**好在哪里**：目标、操作、完成标准分离，可验证。

### 坏

```md
## 流程

分析一下需求，然后看复杂不复杂，再继续写。
```

**坏在哪里**：无判断标准、无完成标准、不可执行。

---

## 决策表样板

### 好

| 情况 | 决策 | 行动 |
|------|------|------|
| 来源不可访问，且用户要求按原文更新规则 | **BLOCK** | 说明无法验证原文，不伪造总结 |
| 复杂度边界不清 | **ASK** | 给出推荐分流，等用户确认 |
| 发现可复用优秀模式 | **LOG** | 纳入 patterns.md，事后汇报 |

### 坏

| 情况 | 行动 |
|------|------|
| 有问题 | 问用户 |
| 没问题 | 继续 |

**坏在哪里**：没有决策级别，没有推荐答案，没有高风险处理。

---

## 外部文章学习协议

### 好

1. 先读取原文或可信来源。
2. 只提炼有证据支撑的设计。
3. 将观点转化为：规则、模板、审计项、pitfall，而不是只做摘要。
4. 来源不可访问时，明确记录不可访问，不伪造文章内容。

### 坏

- 文章打不开仍总结“文章大概说了什么”。
- 只输出读后感，不更新 skill。
- 把未经验证的观点写入硬规则。

---

## 最小可用原则

### 好

简单 skill 只需要：
- description
- quick start
- workflow
- minimal safety rails

复杂 skill 才增加：
- grill
- phase
- BLOCK/ASK/LOG
- 子 agent
- 规则晋升
- patterns / pitfalls

### 坏

任何 skill 都套满复杂治理结构，导致简单任务 token 重、维护难、触发慢。

---

## Harness 治理样板

### 好

```md
## Harness Boundary

- Agent 负责：分析、生成、局部判断。
- Skill 负责：生命周期、工具权限、预算、终止条件、trace、验证标准。

## Stop Conditions

- max_steps: 12
- max_tool_calls: 20
- max_retries_per_step: 2
- max_duration: 30m
```

**好在哪里**：全局控制权不交给模型，复杂任务有硬边界。

### 坏

```md
让 agent 根据情况自己决定要不要继续、要不要重试、要不要调用工具。
```

**坏在哪里**：缺少成本意识、权限意识和终止条件，容易循环、越权或目标偏移。

---

## 状态与记忆样板

### 好

```md
- Working State：当前步骤临时上下文，任务结束丢弃。
- Execution Log：不可变执行日志，用于审计和回放。
- Memory：跨任务复用的偏好、规则、踩坑经验。
```

### 坏

```md
把本次任务遇到的所有路径、输出、中间状态都写入 memory。
```

**坏在哪里**：临时状态污染长期记忆，后续检索噪声增加。

---

## 轨迹评估样板

### 好

复杂 skill 完成时同时检查：
- 最终结果是否满足目标
- 工具调用是否必要且合规
- 是否重复调用或无意义重试
- 是否触发预算/终止条件
- 是否保留 trace 供回放

### 坏

只看最后输出是否“看起来对”。

**坏在哪里**：可能掩盖未授权工具、错误检索、偶然重试成功和目标偏移。

---

## EVOLVE 自训练样板

### 好

```md
## Evolve Setup

- baseline: current checkpoint
- dev: 40 cases
- holdout: 10 cases
- regression: 15 cases
- primary metric: pass_rate
- gate: correctness AND regression AND safety AND cost AND atomicity
```

每轮：看 trace → 提出一个原子改动 → checkpoint → 三层评测 → AND 门控 → keep/discard。

**好在哪里**：优化有数据、有证据、有回滚，不靠作者直觉。

### 坏

```md
我手工试了几个问题，感觉这个 skill 好了一点。
```

**坏在哪里**：没有 GT、没有 holdout、没有 regression、没有可复现指标。

---

## Trace 诊断样板

### 好

```md
Case case-041 失败，因为 trace 显示它把“离职后邮箱是否保留”路由到邮箱规则，但 GT 期望引用账号生命周期规则。
如果在触发边界里加入“离职/禁用/账号生命周期”的优先路由，输出会命中正确路径。
```

### 坏

```md
感觉应该加强邮箱相关说明。
```

**坏在哪里**：没有原始执行证据，容易改错方向。

---

## 原子 Mutation 样板

### 好

- 本轮只改 description 的触发词。
- 下一轮再改 SKILL.md 的 phase。
- 再下一轮才改 scripts。

### 坏

- 一轮同时改 description、workflow、脚本和测试。

**坏在哪里**：无法判断收益来自哪一处，失败也难以回滚。

