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

### The failure that looks like progress

An agent works on a task. It edits, builds, reads the logs, edits again, builds again, reads the logs again. A tight, productive loop.

```
edit → build ✓ → read logs → edit → build ✓ → read logs → ...
```

Except:

- the logs are from yesterday's build
- the new build was never deployed to the running system
- the system's actual state has already moved on

Every iteration *looks* like it closes the loop. None of them touch reality. The whole chain is reasoning on a world that no longer exists.

This is **not** "the agent should test more." Tests check whether the code is correct. This is the agent losing track of whether its picture of the world is *current*. A different failure entirely.

### Definition

> **Reality Drift** — the process by which an agent's internal world model gradually diverges from real-world state.

Or, in one line for engineers:

> *The agent stopped checking reality and started trusting itself.*

The root cause has nothing to do with any specific domain:

```
World state  ≠  Agent's belief state
```

An agent acts on its belief state. When that quietly stops matching the world — and nothing forces a re-check — every downstream step inherits the gap.

### What Reality Drift is *not*

The name is sharp only if it excludes things. Reality Drift is **not**:

- **bad code** — the code can be perfectly correct
- **bad reasoning** — the reasoning can be flawless
- **hallucination** — nothing is invented; the inputs were once real
- **missing tests** — more tests don't help if they run against stale state

Reality Drift can happen *even when the reasoning is correct.* The problem isn't the logic — it's that the premises are no longer current. A flawless deduction from an expired fact is still wrong.

### How it shows up

- **Stale logs** — old output treated as current truth
- **Stale builds** — a successful compile mistaken for verified behavior
- **Stale external state** — code changed, the running system never updated
- **Stale assumptions** — a reasoning chain that stays self-consistent after its premises expired

The danger is that none of these look like errors. The agent stays fluent, confident, and internally consistent — while drifting further from reality with every step.

### A general failure mode

Reality Drift is a general failure mode of long-running AI agents. Embedded systems are merely the environment where it becomes impossible to ignore — flash the wrong build and the LED simply stays dark. But the same gap opens anywhere an agent acts on state it can't continuously see:

- a **DevOps / Kubernetes agent** reasoning over a cluster state that has already rescheduled
- a **browser agent** acting on a page that navigated away three steps ago
- a **robotics agent** planning against a world model the sensors have already contradicted
- a **database-migration agent** trusting a schema snapshot taken before the last migration

The root cause is never the hardware. It's `World state ≠ Agent belief state`, and a loop that never re-observes.

### The fix: re-observe, don't derive

The cure is not a smarter model. It is a discipline: **force the agent to periodically re-observe reality instead of deriving it from old assumptions.** Trust runtime evidence over the reasoning chain. When a new observation contradicts the current premise, stop and update the model — don't patch forward on top of it.

This repo ships one concrete implementation of that discipline — a set of agent collaboration rules. They were written for embedded development (where drift is brutal and physical), but the discipline generalizes to any agent workflow that acts on external state it can't continuously see.

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

### The problem is the point; the rules are one implementation

Read this repo top-down: the **problem** (Reality Drift) is the durable part, the **principle** (re-observe, don't derive) is the fix, and the files below are just *one* way to enforce it.

| File | What it is |
|------|------------|
| [`AGENTS.md`](AGENTS.md) | A reference implementation: cross-project global collaboration rules. |
| [`AGENTS.project.md`](AGENTS.project.md) | A generalized project-level rule template (embedded-flavored). |
| [`examples/`](examples/) | "Behavior tests" for the rules — wrong-vs-fixed comparisons, a filled-in `DEBUG_STATE.md`, and path-scoped `AGENTS.md` examples. |
| [`examples/failure-cases.md`](examples/failure-cases.md) | A wall of *real* Reality Drift incidents, dissected. The failure above is CASE-001. |

Nobody remembers your rule #37. People remember **an agent's world model goes stale.** That sentence is the part worth spreading; the rules are just how one person fights it today.

### How to use it

1. Read [`examples/do-vs-dont.md`](examples/do-vs-dont.md) first — it shows the actual agent behavior each rule is meant to stop. Match it against the mistakes your own agent makes.
2. Use `AGENTS.md` as a global agent instruction; delete the clauses you don't need.
3. Copy `AGENTS.project.md` into your repo root, then add path-scoped `AGENTS.md` files in modules that own protocol details, hardware, persistence formats, timing, or verification paths. See [`examples/layered-agents/`](examples/layered-agents/) for what belongs at the root vs. per module.

Don't paste all of it into a small project. Rule density has a cost — keep only the rules that stop a failure you actually hit.

---

## 简体中文

### 看起来像进展的失败

一个 Agent 在做任务。改、构建、读日志、再改、再构建、再读日志。一个紧凑、高产的循环。

```
改 → build ✓ → 读日志 → 改 → build ✓ → 读日志 → ...
```

但是:

- 日志是昨天那次构建留下的
- 新构建从未部署到正在运行的系统上
- 系统的实际状态早就变了

每一轮*看起来*都闭合了。没有一轮真正触碰现实。整条推理链都建立在一个不再存在的世界上。

这**不是**"Agent 应该多测试"。测试检查的是代码对不对。这是 Agent 失去了对"自己眼中的世界是否还是当前的"的感知。完全是另一种故障。

### 定义

> **Reality Drift（现实漂移）** —— Agent 的内部世界模型逐渐偏离真实世界状态的过程。

或者,给工程师一句话:

> *Agent 不再核对现实,开始相信自己。*

根因和任何具体领域都无关:

```
真实世界状态  ≠  Agent 的信念状态
```

Agent 是按它的信念状态行动的。当信念状态悄悄不再匹配世界、又没有任何机制强制它重新核对时,后续每一步都继承了这道裂缝。

### Reality Drift *不是*什么

一个名字只有在能排除东西时才锋利。Reality Drift **不是**:

- **代码烂** —— 代码可以完全正确
- **推理烂** —— 推理可以无懈可击
- **幻觉** —— 没有任何东西是编造的;那些输入曾经都是真的
- **缺测试** —— 测试再多也没用,如果它们跑在过期的状态上

Reality Drift *甚至会在推理完全正确时发生。* 问题不在逻辑 —— 而在前提已经不是当前的了。从一个过期事实出发的完美推导,依然是错的。

### 它如何暴露

- **Stale Logs（过期日志）** —— 旧输出被当成当前事实
- **Stale Builds（过期构建）** —— 把"构建成功"误当成"行为已验证"
- **Stale External State（过期外部状态）** —— 代码改了，正在运行的系统没更新
- **Stale Assumptions（过期假设）** —— 前提已经失效，推理链却依然自洽

危险之处在于：这些都不像错误。Agent 依然流畅、自信、逻辑自洽 —— 却在每一步里离现实更远。

### 一个通用故障模式

Reality Drift 是长时运行 AI Agent 的**通用**故障模式。嵌入式只是让它无法被忽视的环境 —— 烧错构建,灯就是不亮。但只要 Agent 在一个它无法持续观察的状态上行动,同样的裂缝就会裂开:

- **DevOps / Kubernetes agent** 在一个已经重新调度过的集群状态上推理
- **浏览器 agent** 在三步前就已经跳走的页面上操作
- **机器人 agent** 对着一个传感器早已否定的世界模型做规划
- **数据库迁移 agent** 信任一份在上次迁移之前拍下的 schema 快照

根因从来不是硬件。是 `真实世界状态 ≠ Agent 信念状态`,加上一个永不重新观察的循环。

### 解法：重新观察，而不是推导

解药不是更聪明的模型，而是一种纪律：**强制 Agent 周期性地重新观察现实，而不是从旧假设里推导它。** 运行时证据高于推理链。当新观察与当前前提矛盾时，停下来更新模型 —— 不要在错误前提上继续叠补丁。

本仓库提供这套纪律的一个具体实现 —— 一份 Agent 协作规则。它最初为嵌入式开发而写（那里的漂移既残酷又物理化），但这套纪律适用于任何"在无法持续观察的外部状态上行动"的 Agent 工作流。

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

### 问题才是核心；规则只是一种实现

这个仓库请自上而下读：**问题**（Reality Drift）是持久的部分，**原则**（重新观察，而不是推导）是解法，下面的文件只是强制执行它的*一种*方式。

| 文件 | 是什么 |
|------|--------|
| [`AGENTS.md`](AGENTS.md) | 一个参考实现：跨项目通用的全局协作规则。 |
| [`AGENTS.project.md`](AGENTS.project.md) | 通用化的项目级规则模板（带嵌入式色彩）。 |
| [`examples/`](examples/) | 规则的"行为测试" —— 错误 vs 改好对照、填好的 `DEBUG_STATE.md`、目录级 path-scoped `AGENTS.md` 示例。 |
| [`examples/failure-cases.md`](examples/failure-cases.md) | 一面 *真实* Reality Drift 事故墙，逐个解剖。上面那个案例就是 CASE-001。 |

没人会记住你的第 37 条规则。别人记住的是：**Agent 的世界模型会过期。** 这句话才是值得传播的部分；规则只是一个人当下对抗它的方式。

### 使用方式

1. 先读 [`examples/do-vs-dont.md`](examples/do-vs-dont.md) —— 它展示每条规则想拦住的实际 Agent 行为。拿它对照你自己 Agent 常犯的错。
2. 把 `AGENTS.md` 作为全局 agent instruction 使用，删掉用不上的条款。
3. 复制 `AGENTS.project.md` 到你的仓库根目录，再给拥有协议细节、硬件、持久化格式、时序或验证路径的模块补 path-scoped `AGENTS.md`。哪些上移到根、哪些留在模块，参考 [`examples/layered-agents/`](examples/layered-agents/)。

不要把所有条款都搬进小项目。规则密度是有成本的 —— 只保留能拦住你实际遇到的失败模式的那些。
