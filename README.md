# Reality Drift

> An AI agent's context is not reality. Over a long session, the two quietly drift apart.

> AI Agent 的上下文不是现实。会话越长，两者越是悄悄分叉。

**[English](#english)** · **[简体中文](#简体中文)**

<details>
<summary>💼 The infomercial version · 听听律师怎么说</summary>

<br>

> Did you — or a loved one — watch your AI agent run **THREE rounds** of
> optimization on firmware it never flashed? You are **NOT** alone.
>
> They call it **Reality Drift**, and your agent's got it *bad*. Stale logs?
> Phantom builds? A reasoning chain so smooth it forgot to check whether the
> device was even **ON**?
>
> Hey — that's not your fault. *(points at camera)*
>
> I've got a workflow for that. Re-observe reality, stop trusting the green
> checkmark, and never optimize a ghost again. No win, no fee.
>
> Don't just *hope* your agent's in sync. **Better Check Reality.™**

> 你 —— 或者你的 Agent —— 还在被**现实漂移**折磨吗?
> 代码改了三轮、构建次次成功,结果固件压根没烧进去?
>
> 这不怪你!这病叫 **Reality Drift**,市面上十个 Agent 九个中招!
> 过期日志、幻觉构建、一条顺到忘了问"设备开没开"的推理链 ——
>
> 别让一个绿色的 ✓ 毁了你的下午。一套规则,重新观察现实,
> 假象退散,**不好使不要钱**。还在等?现实同步,刻不容缓。™

---

*…and yes, that pitch was exactly the kind of smooth, confident claim this repo
teaches you to distrust. So don't take my word for it — go check your device.
That's the whole point.*

*…当然,你刚刚听的,就是那种"自信、顺滑、你不该全信"的推销腔。所以 ——
别信我这段广告,去核对你的设备状态。这正是本仓库要讲的事。*

</details>

---

## English

### A real failure

An agent was optimizing embedded firmware. It:

- edited the code
- built successfully
- ran three rounds of optimization

What actually happened:

- the firmware was never flashed to the device
- the agent kept analyzing logs from an earlier build
- all three rounds rested on a premise that was already false

The agent's coding ability was never the problem. It had simply lost track of the real state of the world.

### This is Reality Drift

**Reality Drift** is the widening gap between what an agent *believes* the world state is and what it *actually* is.

It shows up as:

- **Stale logs** — old output treated as current truth
- **Stale builds** — a successful compile mistaken for verified behavior
- **Stale hardware state** — code changed, device never updated
- **Stale assumptions** — a reasoning chain that stays self-consistent after its premises expired

The danger is that none of these look like errors. The agent stays fluent, confident, and internally consistent — while drifting further from reality with every step.

### The fix: re-observe, don't derive

The cure is not a smarter model. It is a discipline: **force the agent to periodically re-observe reality instead of deriving it from old assumptions.** Trust runtime evidence over the reasoning chain. When a new observation contradicts the current premise, stop and update the model — don't patch forward on top of it.

This repo ships one concrete implementation of that discipline — a set of agent collaboration rules. It was written for embedded development (where drift is brutal and physical), but most of it generalizes to any agent workflow that depends on external state it can't see directly.

The whole idea in two pictures.

**Right — the loop closes.** Every few steps, reasoning returns to reality and re-observes:

```
  ┌──────────────────────┐
  ▼                      │   re-observe,
Reality                  │   every few steps
  │ observe              │
  ▼                      │
Evidence                 │
  │ update               │
  ▼                      │
Agent model              │
  │ reason               │
  ▼                      │
Reasoning ───────────────┘
```

**Wrong — the line leaves reality and never comes back. That's drift:**

```
Reality              ← seen once, then left behind
  ╳
  ┊
Agent model
  │ reason
  ▼
Reasoning
  │ keeps reasoning on its own output
  ▼
Reasoning
  │
  ▼
Reasoning   →→→   drift
```

### Files

| File | What it is |
|------|------------|
| [`AGENTS.md`](AGENTS.md) | A reference implementation: cross-project global collaboration rules. |
| [`AGENTS.project.md`](AGENTS.project.md) | A generalized project-level rule template (embedded-flavored). |
| [`examples/`](examples/) | "Behavior tests" for the rules — wrong-vs-fixed comparisons, a filled-in `DEBUG_STATE.md`, and path-scoped `AGENTS.md` examples. |
| [`examples/failure-cases.md`](examples/failure-cases.md) | A wall of *real* Reality Drift incidents, dissected. The failure above is CASE-001. |

These rules are *an* answer, not *the* answer. The problem above is the durable part — the rules are just how one person fights it today.

### How to use it

1. Read [`examples/do-vs-dont.md`](examples/do-vs-dont.md) first — it shows the actual agent behavior each rule is meant to stop. Match it against the mistakes your own agent makes.
2. Use `AGENTS.md` as a global agent instruction; delete the clauses you don't need.
3. Copy `AGENTS.project.md` into your repo root, then add path-scoped `AGENTS.md` files in modules that own protocol details, hardware, persistence formats, timing, or verification paths. See [`examples/layered-agents/`](examples/layered-agents/) for what belongs at the root vs. per module.

Don't paste all of it into a small project. Rule density has a cost — keep only the rules that stop a failure you actually hit.

---

## 简体中文

### 一个真实失败案例

一个 Agent 在优化嵌入式固件。它：

- 修改了代码
- 构建成功
- 连续跑了三轮优化

而实际情况是：

- 固件根本没有烧录到设备上
- Agent 一直在分析上一次构建留下的旧日志
- 三轮修改全部建立在一个早已失效的前提上

问题从来不是代码能力。它只是失去了对真实世界状态的感知。

### 这就是 Reality Drift

**Reality Drift（现实漂移）** 指 Agent *相信* 的世界状态，与世界 *实际* 状态之间不断扩大的偏离。

它表现为：

- **Stale Logs（过期日志）** —— 旧输出被当成当前事实
- **Stale Builds（过期构建）** —— 把"构建成功"误当成"行为已验证"
- **Stale Hardware State（过期硬件状态）** —— 代码改了，设备没更新
- **Stale Assumptions（过期假设）** —— 前提已经失效，推理链却依然自洽

危险之处在于：这些都不像错误。Agent 依然流畅、自信、逻辑自洽 —— 却在每一步里离现实更远。

### 解法：重新观察，而不是推导

解药不是更聪明的模型，而是一种纪律：**强制 Agent 周期性地重新观察现实，而不是从旧假设里推导它。** 运行时证据高于推理链。当新观察与当前前提矛盾时，停下来更新模型 —— 不要在错误前提上继续叠补丁。

本仓库提供这套纪律的一个具体实现 —— 一份 Agent 协作规则。它最初为嵌入式开发而写（那里的漂移既残酷又物理化），但其中大部分适用于任何依赖"看不见的外部状态"的 Agent 工作流。

整套思路两张图就够。

**对 —— 环闭合。** 每隔几步,推理会回到现实重新观察:

```
  ┌──────────────────────┐
  ▼                      │   每隔几步
Reality                  │   重新观察
  │ observe              │
  ▼                      │
Evidence                 │
  │ update               │
  ▼                      │
Agent model              │
  │ reason               │
  ▼                      │
Reasoning ───────────────┘
```

**错 —— 线离开现实,再也不回头。这就是 drift:**

```
Reality              ← 只看了一次,之后被丢下
  ╳
  ┊
Agent model
  │ reason
  ▼
Reasoning
  │ 在自己的结论上继续推
  ▼
Reasoning
  │
  ▼
Reasoning   →→→   drift
```

### 文件

| 文件 | 是什么 |
|------|--------|
| [`AGENTS.md`](AGENTS.md) | 一个参考实现：跨项目通用的全局协作规则。 |
| [`AGENTS.project.md`](AGENTS.project.md) | 通用化的项目级规则模板（带嵌入式色彩）。 |
| [`examples/`](examples/) | 规则的"行为测试" —— 错误 vs 改好对照、填好的 `DEBUG_STATE.md`、目录级 path-scoped `AGENTS.md` 示例。 |
| [`examples/failure-cases.md`](examples/failure-cases.md) | 一面 *真实* Reality Drift 事故墙，逐个解剖。上面那个案例就是 CASE-001。 |

这些规则是 *一个* 答案，不是 *唯一* 答案。上面那个问题才是持久的部分 —— 规则只是一个人当下对抗它的方式。

### 使用方式

1. 先读 [`examples/do-vs-dont.md`](examples/do-vs-dont.md) —— 它展示每条规则想拦住的实际 Agent 行为。拿它对照你自己 Agent 常犯的错。
2. 把 `AGENTS.md` 作为全局 agent instruction 使用，删掉用不上的条款。
3. 复制 `AGENTS.project.md` 到你的仓库根目录，再给拥有协议细节、硬件、持久化格式、时序或验证路径的模块补 path-scoped `AGENTS.md`。哪些上移到根、哪些留在模块，参考 [`examples/layered-agents/`](examples/layered-agents/)。

不要把所有条款都搬进小项目。规则密度是有成本的 —— 只保留能拦住你实际遇到的失败模式的那些。
