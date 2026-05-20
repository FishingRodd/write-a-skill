# Evolve Protocol for Skills

用于把 skill 当作可训练对象，通过 GT、trace、eval、gate、rollback 做持续优化。普通一次性 skill 不必套用。

---

## 核心原则

Skill 不是只靠手工打磨的文档，而是可以被评测、诊断、回滚、选优的对象。

目标不是让语法通过，而是让 skill 行为匹配目标数据分布。

---

## 适用信号

出现任一信号，应考虑 EVOLVE：

- 用户提供 GT / 测试集 / 评测标准
- 需要提升召回率、通过率、命中率、格式合规率、成本指标
- skill 已能跑，但“不够好用”
- 修改后担心破坏旧 case
- 需要多轮迭代、自动评测、回滚、选最佳 checkpoint

---

## 工作区要求

EVOLVE 必须在隔离环境中执行：

- 独立 git branch / worktree / workspace
- 每轮改动可 checkpoint
- 每轮失败可 revert
- 不直接污染生产 skill

---

## GT 数据结构

推荐 JSONL：

```json
{"id":"case-001","input":"用户请求文本","expected":{"contains":["关键短语"],"path_hit":["docs/foo.md"],"fact_coverage":["事实1","事实2"]},"tags":["routing","regression"]}
```

### 常见 assertion

| Assertion | 类型 | 说明 |
|-----------|------|------|
| contains | deterministic | 输出必须包含文本 |
| not_contains | deterministic | 输出不得包含文本 |
| regex | deterministic | 正则匹配 |
| path_hit | semantic | 是否命中正确引用/路径 |
| fact_coverage | semantic | 是否覆盖关键事实 |
| script_check | deterministic | 执行脚本校验 |
| schema_check | deterministic | 结构化输出校验 |
| llm_yes_no | semantic | LLM 做 YES/NO 判断 |

确定性优先；LLM 判断只用于语义覆盖、表达质量等非确定性项。

---

## 数据拆分

| Split | 用途 | 规则 |
|-------|------|------|
| dev | 每轮优化 | optimizer 可见 |
| holdout | 泛化验证 | optimizer 不可见，只在 Strict Eval 跑 |
| regression | 防回退 | 历史成功/脆弱 case |
| impossible | 不可修 case | GT 有争议或目标本身不成立 |

连续多轮无法修复的 case，先怀疑 GT，而不是盲目硬编码。

---

## evolve_plan.md 模板

```md
# Evolve Plan

## Target Skill

- path: {{skill_path}}
- baseline: {{baseline_commit}}

## Metric

- primary: {{pass_rate / recall / cost / format_compliance}}
- secondary: {{token / latency / tool_calls}}

## Dataset

- dev: {{N}}
- holdout: {{N}}
- regression: {{N}}

## Gates

- correctness: no decrease / improve target cases
- regression: 100% existing guards pass
- safety: no critical safety violation
- cost: no unacceptable growth
- atomicity: one change per iteration

## Budget

- max_iterations: {{N}}
- max_no_keep_per_layer: {{K}}
- max_cost: {{budget}}

## Mutation Layers

- L1: description / trigger / examples
- L2: SKILL.md workflow / decision table / safety rails
- L3: scripts / references / templates / harness
```

---

## 8 阶段 Loop

### Phase 0：Setup

一次性执行：

- 检查 skill 结构
- 检查 workspace/git 状态
- 建立 baseline
- 准备 GT split
- 生成 evolve_plan.md

### Phase 1：Review

读取：

- 最近 git log
- results.tsv
- experiments.jsonl
- 上一轮失败 case
- per-case trace

提取 5 类信号：

1. 成功过的改法
2. 失败过的改法
3. 持续失败的 case
4. 脆弱 case
5. 是否卡住

### Phase 2：Ideate

必须基于 trace 做反事实诊断：

```md
Case {{id}} 失败，因为 {{trace evidence}}。
如果修改 {{change}}，输出应从 {{old_behavior}} 变为 {{new_behavior}}。
```

没有 trace 证据，不许提出改动。

### Phase 3：Modify

每轮只做 ONE 个原子改动。

原子性判断：

- 描述中出现“和” → 拆轮
- 涉及多个文件且没有强理由 → 拆轮
- git diff --stat 超出阈值 → 拆轮

### Phase 4：Checkpoint

先保存 checkpoint，再验证：

- commit / patch / snapshot
- 记录 mutation 层和变更意图
- 确保可 revert

### Phase 5：Verify

跑三层评测：

| 层级 | 成本 | 触发 | 内容 |
|------|------|------|------|
| L1 Quick Gate | 秒级 | 每轮 | 结构、格式、安全、smoke |
| L2 Dev Eval | 分钟级 | 每轮 | dev 全量 GT |
| L3 Strict Eval | 高 | 条件触发 | holdout、regression、blind A/B |

L1 失败直接 discard，避免浪费成本。

### Phase 6：Gate

5 维 AND 门控：

| Gate | 要求 |
|------|------|
| Correctness | 主指标不下降，目标 case 改善 |
| Regression | regression 不坏 |
| Safety | 无 critical 安全违规 |
| Cost | token/tool/file complexity 不可接受增长为 NO |
| Atomicity | 本轮只有一个可解释改动 |

任一 NO → discard / revert。

### Phase 7：Log

写入：

- results.tsv
- experiments.jsonl
- per-case trace
- keep/discard reason
- cost metrics

### Phase 8：Loop

- 连续 K 轮无 keep → 当前 mutation 层 exhausted，升层
- 连续多轮失败 → 切换策略或停止
- 三层都无改善 → 停止并输出最终报告

---

## results.tsv 格式

```tsv
iteration	checkpoint	layer	status	dev_score	holdout_score	regression_score	cost	changed_files	reason
1	abc123	L1	keep	0.82	-	1.00	low	1	improved routing triggers
2	def456	L1	discard	0.80	-	1.00	low	1	correctness decreased
```

---

## experiments.jsonl 格式

```json
{"iteration":1,"layer":"L1","hypothesis":"adding trigger X will route case-041 correctly","trace_refs":["traces/case-041.json"],"change":"update description trigger","gate":{"correctness":true,"regression":true,"safety":true,"cost":true,"atomicity":true},"decision":"keep"}
```

---

## per-case trace 结构

```json
{
  "case_id": "case-041",
  "input": "...",
  "expected": {...},
  "actual": "...",
  "tool_calls": [],
  "memory_used": [],
  "decision_path": ["matched trigger", "selected workflow", "generated output"],
  "assertions": [
    {"name":"path_hit","pass":false,"reason":"expected docs/address-book.md but got docs/mail.md"}
  ]
}
```

Trace 不必全塞进 prompt；给路径，让 proposer 按需读取原始证据。

---

## 最终报告模板

```md
# Evolve Report

## Summary

- best checkpoint: {{hash}}
- iterations: {{N}}
- kept: {{N}}
- discarded: {{N}}

## Metrics

| metric | baseline | best | delta |
|--------|----------|------|-------|
| dev pass_rate | | | |
| holdout pass_rate | | | |
| regression pass_rate | | | |
| cost | | | |

## Kept Changes

- {{iteration}}: {{change}} — {{why kept}}

## Discarded Changes

- {{iteration}}: {{change}} — {{gate failed}}

## Unfixable / Questionable GT

- {{case_id}}: {{reason}}

## Next Steps

- {{recommendation}}
```

---

## Safety Rails

**NEVER**：
- 没有 GT / 评测标准就声称完成 EVOLVE
- 没有 trace 证据就修改 skill
- 一轮做多个独立改动
- 用 dev 集表现替代 holdout 泛化验证
- 为了涨分硬编码 case
- LLM 评测只跑一次就判断微小提升有效
- 坏改动不 rollback

**ALWAYS**：
- 先 baseline，再迭代
- 先低成本 mutation 层，必要时再升层
- 每轮写结果和 trace
- L1 失败直接 discard
- keep/discard 必须给出 gate 理由
- 对长期失败 case 允许标记为 GT 问题或不可修
