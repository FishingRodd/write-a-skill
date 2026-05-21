---
# Complex Skill Template
# 适用于：多阶段 workflow、有决策边界、有子 agent、预估文档 > 100 行
---

```markdown
---
name: {{skill-name}}
description: {{一句话说明 capability，第三人称}}。Use when: {{具体触发词/场景，2-3个}}。Do NOT use for: {{明确排除场景}}。
---

# {{Skill Name}}

## 概览

<!-- 用一张表说清模式/触发/入口 -->
| 模式 | 触发条件 | 入口 |
|------|---------|------|
| **{{模式A}}** | {{触发条件}} | → Phase {{N}} |
| **{{模式B}}** | {{触发条件}} | → Phase {{N}} |

---

## Phase 1：{{阶段名称}}

<!-- 每个 Phase 必须有：目标 + 操作 + 完成标准 + Governance Gate -->

**目标**：{{本阶段要达成的单一目标}}

**操作**：
- {{步骤 1}}
- {{步骤 2}}

**完成标准**：{{本阶段产物或判断结果达到什么状态}}

**Governance Gate**：{{进入下一阶段前必须满足的条件；可包含 BLOCK 条件、审计项或 smoke 验证}}

---

## Phase 2：{{阶段名称}}

**目标**：{{...}}

**操作**：
- {{...}}

**完成标准**：{{...}}

**Governance Gate**：{{...}}

---

<!-- 根据实际需要增加 Phase，长任务必须拆分，防止目标偏移 -->

---

## 治理设计

| 机制 | 权威位置 | 验证方式 |
|------|----------|----------|
| Source of Truth | {{哪一个文件定义硬规则}} | {{如何检查规则未漂移}} |
| Governance Gates | {{Phase / 决策表位置}} | {{如何检查每个 gate 可执行}} |
| Context Layers | {{Core / Domain / Task 的归属}} | {{如何避免 Task 规则污染 Core}} |
| Traceability | {{记录目标→决策→改动→验证的位置}} | {{如何回放或审计}} |
| Self-Refinement Sink | {{PATTERN/RULE/TEMPLATE/EVAL/PITFALL 去向}} | {{如何判断晋升或降级}} |

---

## BLOCK / ASK / LOG 决策表

| 情况 | 决策 | 行动 |
|------|------|------|
| {{情况描述}} | **BLOCK** | {{停止并说明原因}} |
| {{情况描述}} | **ASK** | {{给出推荐，等用户确认}} |
| {{情况描述}} | **LOG** | {{自主处理，事后汇报}} |

---

## 规则晋升机制

用 `[OBSERVE: <描述>]` 标记会话内遇到的边界情况。

| 情况 | 晋升路径 |
|------|---------|
| 同一 LOG 情况出现 ≥ 3 次 | 升为 ASK，写入决策表 |
| 同一 ASK 推荐被接受 ≥ 3 次 | 升为 AUTO，标注前提条件 |
| AUTO 判断被纠正 | 降为 REVIEW，写入 `pitfalls.md` |

会话结束 → 触发 neat-freak 审查所有 `[OBSERVE]` 标记。

---

## Safety Rails

**NEVER**：
- {{绝对禁止操作 1（动词开头，操作级别）}}
- {{绝对禁止操作 2}}

**ALWAYS**：
- {{必须执行操作 1}}
- {{必须执行操作 2}}

---

## 参考资料

<!-- 按需加载，节省 token -->
| 文件 | 内容 | 读取时机 |
|------|------|---------|
| `pitfalls.md` | 已知踩坑和修复 | 遇到报错/异常时 |
| `examples.md` | 可复用命令/代码片段 | 需要具体示例时 |
| `runbook.md` | 完整 SOP | 需要详细流程时 |
| `harness.md` | 多 agent / MCP / 高风险工具 / 预算 / trace 治理 | 涉及生产级 agent 编排时 |
| `monitor-agent.md` | 子 agent 行为规范 | 启动子 agent 时 |

---

## 支撑文件结构

```
{{skill-name}}/
├── SKILL.md              # 本文件，流程入口和硬规则索引
├── patterns.md           # 好/坏设计样板（按需添加）
├── pitfalls.md           # 踩坑记录（初始留空）
├── examples.md           # 可复用片段（按需添加）
├── runbook.md            # 详细 SOP（按需添加）
├── harness.md            # 多 agent / MCP / 高风险工具 / 预算 / trace 治理（按需添加）
├── monitor-agent.md      # 子 agent 规范（有子 agent 时）
└── scripts/              # deterministic 操作脚本
    └── {{script-name}}.sh
```
```

---

## 使用说明

| 占位符 | 要求 |
|--------|------|
| `description` | ≤ 1024 字符，必含 "Use when" 和 "Do NOT use for" |
| `Phase` | 每个 Phase 必须有完成标准和 Governance Gate，长任务必须拆分（防止目标偏移） |
| `治理设计` | 必须说明 Source of Truth、Context Layers、Traceability 和 Self-Refinement Sink |
| `BLOCK/ASK/LOG` | 每种决策类型至少 1 条，覆盖最高风险场景 |
| `NEVER` | 至少 2 条，必须是操作级别（"不要做某事"而非"要小心"） |
| `规则晋升` | 如 skill 会被频繁调用，必须包含此节 |

**Progressive Disclosure 分档规则**：
- SKILL.md 超 100 行 → 拆出支撑文件
- 可复用代码/命令重复 ≥ 3 次 → 提取到 `examples.md`
- 完整 SOP → 提取到 `runbook.md`
- 有子 agent → 单独 `monitor-agent.md`
- 涉及多 agent / MCP / 高风险工具 / 预算 / trace → 单独 `harness.md`
