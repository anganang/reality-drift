# Examples

这些示例是规则的"行为测试"——展示规则约束的实际 Agent 行为长什么样，方便复用者裁剪而不是把整份规则原样塞进项目。

- [`do-vs-dont.md`](do-vs-dont.md)：错误行为 vs 改好行为的对照。每条对应根 `AGENTS.md` 的一条规则，是理解这套规则到底想拦住什么的最快入口。
- [`failure-cases.md`](failure-cases.md)：**真实** Reality Drift 事故档案，逐个解剖（Agent 相信什么 vs 现实、裂缝从哪裂开、本该在哪停下、代价）。和 `do-vs-dont.md` 互补——后者是合成场景教"怎么做对"，这里是真实事件做"事后解剖"。
- [`DEBUG_STATE.example.md`](DEBUG_STATE.example.md)：一份填好的 `DEBUG_STATE.md`，演示"状态检查点"该有的密度（≤15 行），不是调试日记。
- [`layered-agents/`](layered-agents/)：目录级 path-scoped `AGENTS.md` 示例——根规则 + 一个驱动模块的局部规则，演示什么该上移到根、什么该留在模块目录。

## 怎么用

1. 先读 `do-vs-dont.md`，对照自己项目里 Agent 常犯的错，判断哪几条规则对你最相关。
2. 把根 `AGENTS.md` 作为全局 instruction，按需删掉用不上的条款。
3. 复制 `AGENTS.project.md` 到项目根，按 `layered-agents/` 的模式给有独立状态/协议/硬件的模块补 path-scoped `AGENTS.md`。

不要把所有条款都搬进小项目。规则密度是有成本的——只保留能拦住你实际遇到的失败模式的那些。
