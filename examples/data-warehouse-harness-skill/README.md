# 数仓 Harness demo

> 下面是示例，不是通用约束。

## 可复用的通用设计

- **持久化层**：把会话状态写进 `.claude/CLAUDE.md`
- **验证层**：用 hooks 在写入后自动检查
- **隔离层**：把高 token 读取和验证放进 subagent
- **治理层**：定义预算、终止条件、trace

## 领域样例

- `sql-validator`：只负责验证 SQL 结构与规范
- `dw-explorer`：只负责读取表结构和血缘摘要
- `data-quality-checker`：只负责自测结果汇总

## 这个 demo 要传达的点

- 领域内容可以很具体
- 但写进通用 skill 时，只保留“怎么设计”的方法
- 具体行业规则应放在 example/demo，而不是硬规则
