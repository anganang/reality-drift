# 嵌入式 Agent 规则

这是我的个人 `AGENTS.md` 规则，用于约束 AI 编程助手的协作方式。重点面向嵌入式开发、运行时证据、回滚边界和基于证据的调试。

## 文件

- `AGENTS.md`：跨项目通用的全局协作规则。
- `AGENTS.project.md`：通用化后的嵌入式项目级规则模板。

## 使用方式

将 `AGENTS.md` 作为全局 agent instruction 使用。把 `AGENTS.project.md` 复制或改写到具体仓库根目录，再根据项目实际情况，在拥有协议细节、硬件资源、持久化格式、时序约束或验证路径的模块目录下补充 path-scoped `AGENTS.md`。
