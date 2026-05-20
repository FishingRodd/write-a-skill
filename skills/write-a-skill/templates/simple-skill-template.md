---
# Simple Skill Template
# 适用于：单一 workflow，无子 agent，预估文档 < 100 行
---

```markdown
---
name: {{skill-name}}
description: {{一句话说明 capability}}。Use when: {{具体触发词/场景，2-3个}}。Do NOT use for: {{明确排除场景}}。
---

# {{Skill Name}}

## Quick Start

<!-- 最小可工作示例，3 行以内 -->
{{示例命令或操作}}

## Workflow

1. **{{步骤1}}** — {{说明}}
2. **{{步骤2}}** — {{说明}}
3. **{{步骤3}}** — {{说明}}

## Safety Rails

**NEVER**:
- {{绝对禁止操作 1}}
- {{绝对禁止操作 2}}

**ALWAYS**:
- {{必须执行操作 1}}

## 参考

- {{相关文档或路径（如有）}}
```

---

## 使用说明

填写时替换所有 `{{}}` 占位符：

| 占位符 | 要求 |
|--------|------|
| `description` | ≤ 1024 字符，必含 "Use when" 和 "Do NOT use for" |
| `Quick Start` | 用户看到即可上手，不超过 3 行 |
| `Workflow` | 步骤数 3-7 个，每步一句话说清目标 |
| `NEVER` | 至少 1 条，操作级别（动词开头） |
| `ALWAYS` | 如无强制操作可省略此节 |
