# write-a-skill

> 一个用于设计、审计、学习和进化 Claude Code Skill 的生产级工程框架。

`write-a-skill` 帮你把一个粗糙的自动化想法，变成一个**边界清晰、可审计、可评测、可演进**的 Claude Code Skill。

大多数 skill 生成器只停留在生成 `SKILL.md`。

`write-a-skill` 更进一步：

- **NEW** — 从业务或工程流程创建新 skill
- **AUDIT** — 用结构化 checklist 审计已有 skill
- **LEARN** — 从文章、agent session、其他 skill 中吸收优秀模式
- **EVOLVE** — 用 GT、eval、trace、gate、rollback 持续优化 skill

如果你在做 DevOps、内部工具、客服支持、数据流程、安全运营、部署自动化或垂直业务流程的 Claude Code Skill，这个项目提供的是一套可复用的工程框架，而不是又一个 prompt 模板。

---

## 为什么需要它

Claude Code Skill 很容易写出第一版，但很难稳定可靠。

一个好 skill 不是一段 prompt，而需要：

- 清晰触发边界
- 安全规则
- 分阶段 workflow
- 决策表
- 渐进式上下文披露
- 可追踪工具调用
- 评测用例
- 回归保护
- 从失败中学习的机制

`write-a-skill` 把 skill 当成工程对象：

```text
提示词文档 → 受治理的 workflow → 可审计 harness → 可演进系统
```

---

## 核心能力

### 1. NEW：创建新 Skill

简单需求直接生成最小可用 skill；复杂需求先做设计访谈，明确：

- 触发边界
- Safety Rails
- Phase 拆分
- BLOCK / ASK / LOG 决策
- 状态与记忆
- 轨迹评估
- 预算与降级
- 子 agent 需求

### 2. AUDIT：审计已有 Skill

内置 checklist 检查：

- description 是否清晰
- 是否有 NEVER / ALWAYS
- 是否有 Phase 和完成标准
- 是否有决策表
- 是否有知识沉淀路径
- 是否合理拆分文件
- 是否具备 harness 级治理
- 是否具备 eval / trace / regression 能力

### 3. LEARN：吸收外部设计

从文章、优秀 agent session、其他 skill 中提炼模式，并落到：

- `SKILL.md`
- `audit-checklist.md`
- `patterns.md`
- `harness.md`
- `evolve.md`
- `pitfalls.md`
- `templates/`

### 4. EVOLVE：让 Skill 自我进化

高级模式支持：

- GT 数据集
- dev / holdout / regression 拆分
- per-case trace
- L1 / L2 / L3 三层评测
- Correctness / Regression / Safety / Cost / Atomicity 五维 AND 门控
- checkpoint / rollback

---

## 安装

```bash
cp -r skills/write-a-skill ~/.claude/skills/
```

在 Claude Code 中使用：

```text
/write-a-skill 帮我创建一个客服工单分流 skill
/write-a-skill 审计这个部署排障 skill
/write-a-skill 学习这篇文章并更新 skill 设计
/write-a-skill 用这些 GT case 训练这个 skill
```

---

## 仓库结构

```text
write-a-skill/
├── README.md
├── README.zh-CN.md
├── LICENSE
├── skills/
│   └── write-a-skill/
│       ├── SKILL.md
│       ├── audit-checklist.md
│       ├── patterns.md
│       ├── harness.md
│       ├── evolve.md
│       ├── pitfalls.md
│       └── templates/
│           ├── simple-skill-template.md
│           └── complex-skill-template.md
└── examples/
    ├── data-warehouse-harness-skill/
    │   ├── SKILL.md
    │   ├── harness.md
    │   ├── subagents.md
    │   └── pitfalls.md
    └── it-deploy-troubleshooting-skill/
        ├── SKILL.md
        ├── runbook.md
        ├── monitor-agent.md
        ├── pitfalls.md
        ├── harness.md
        └── eval/
            ├── gt.dev.jsonl
            ├── gt.holdout.jsonl
            └── gt.regression.jsonl
```

---

## 示例：IT 部署排障 Skill

示例位于：

```text
examples/it-deploy-troubleshooting-skill/
```

它展示了一个通用 IT 运维场景：

- 生成部署步骤
- 排查服务部署失败
- 检查日志、端口、配置
- 远程操作安全边界
- 后台 monitor agent 协议
- 用 GT case 做后续进化

示例已做通用化，不包含私有基础设施信息。

---

## 适合谁

适合你，如果你正在：

- 给内部工程团队构建 Claude Code Skill
- 把 SOP 转成 agent workflow
- 设计垂直业务 AI agent
- 让 DevOps / 安全 / 支持 / 数据流程自动化变得可靠
- 避免 prompt 越写越乱
- 想要一套可复用的 skill 审计和改进方法

---

## SEO 关键词

Claude Code Skill、Claude Code skill creator、AI Agent Skill 框架、智能体工作流设计、Prompt Engineering、Context Engineering、Multi-Agent Harness、MCP 工具治理、AI Agent Eval、LLM Eval、Claude Code 自动化、DevOps AI Agent、部署排障 AI、自进化 Skill。

---

## 为什么值得 Star

- 不只是模板，而是一套 skill 工程框架
- 支持简单业务流程，也支持复杂生产流程
- 内置审计 checklist 和好坏样板
- 引入 harness 思维，适合生产级 agent workflow
- 引入 EVOLVE 协议，让 skill 可以用数据持续优化
- 提供贴近 IT 场景的部署排障示例

---

## License

MIT
