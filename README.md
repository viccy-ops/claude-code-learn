# Claude Code Source Study Repository
# Claude Code 源码学习仓库

> This repository is centered on Claude Code's original `src/` codebase rather than product packaging.
>
> The goal is to study Claude Code directly from source: its architecture, tool system, permission model, terminal interaction patterns, and the agent engineering ideas behind it.
>
> 这个仓库的重点不是产品包装，而是 Claude Code 的原始 `src/` 代码本体。
>
> 目标是帮助开发者直接从源码理解 Claude Code 的架构拆分、工具系统、权限模型、CLI 交互方式，以及它背后的 agent engineering 设计理念。

---

## Quick Navigation
## 快速导航

- [Repository Purpose / 仓库定位](#repository-purpose--仓库定位)
- [What You Can Learn / 你能从这里学到什么](#what-you-can-learn--你能从这里学到什么)
- [What's In This Repo / 当前仓库内容](#whats-in-this-repo--当前仓库内容)
- [Recommended Reading Order / 推荐阅读顺序](#recommended-reading-order--推荐阅读顺序)
- [Must-Read Files / 必读文件](#must-read-files--必读文件)
- [Modules Worth Studying / 值得重点研究的模块](#modules-worth-studying--值得重点研究的模块)
- [Core Design Ideas / Claude Code 的核心设计理念](#core-design-ideas--claude-code-的核心设计理念)
- [Forking Checklist / 二开清单](#forking-checklist--二开清单)
- [How To Study This Codebase / 建议的源码学习方法](#how-to-study-this-codebase--建议的源码学习方法)
- [Questions Worth Taking Notes On / 适合重点做笔记的问题](#questions-worth-taking-notes-on--适合重点做笔记的问题)
- [Practical Conclusion / 一个更务实的结论](#practical-conclusion--一个更务实的结论)
- [Disclaimer / 免责声明](#disclaimer--免责声明)

---

## Repository Purpose
## 仓库定位

If you want to learn Claude Code seriously, the highest-value material is not the outer narrative. It is the source layout itself:

- how it turns model capabilities into callable `tools`
- how it builds a maintainable terminal UI with React + Ink
- how it balances execution power with permission boundaries
- how it handles multi-agent coordination, context compression, task resume, IDE bridge, skills, MCP, and LSP

This repository is useful because it reduces the noise from product packaging, hosted configuration, and marketing framing, so you can focus on the implementation itself.

The emphasis here is:

- learning from source
- understanding architecture
- extracting design tradeoffs
- reusing practical agent CLI patterns

not focusing on official platform operations or account-layer concerns.

如果你真正想学 Claude Code，最有价值的不是外围介绍，而是它的原始代码组织方式：

- 它如何把大模型能力拆成可调用的 `tools`
- 它如何把终端交互做成 React + Ink 的可维护 UI
- 它如何在“自由执行”与“权限约束”之间做平衡
- 它如何处理多代理协作、上下文压缩、任务恢复、IDE bridge、skills、MCP、LSP 等复杂能力

这个仓库的意义，在于尽可能减少官方产品化包装、远程托管配置、营销叙事对学习过程的干扰，让你直接观察 Claude Code 的实现本体。

这里强调的是：

- 学源码
- 看架构
- 理解设计取舍
- 提炼可复用的 agent CLI 设计模式

而不是把注意力放在官方平台策略、账号体系或外部运营层。

---

## What You Can Learn
## 你能从这里学到什么

Claude Code is not just a shell around an API. It is closer to a full agent runtime:

- the top layer is commands, terminal UI, and interaction state
- the middle layer is the query engine, tool loop, context handling, and task orchestration
- the lower layer is capability integration: shell, filesystem, web, MCP, LSP, IDE, and remote sessions

This codebase is especially valuable if you are building:

- AI coding agents
- terminal-native developer agents
- tool-calling runtimes
- multi-agent systems
- AI products for engineering workflows
- local execution agents with permission control
- skills / plugins / MCP extension systems

Claude Code 并不是“一个会调 API 的命令行壳”，它本质上是一个完整的 agent runtime：

- 上层是命令系统、终端 UI、交互状态机
- 中层是 query engine、tool loop、上下文管理、任务编排
- 下层是 shell、文件系统、web、MCP、LSP、IDE、remote session 等能力接入

如果你正在做下面这些方向，这份源码很值得系统阅读：

- AI Coding Agent
- Terminal Agent / CLI Copilot
- Tool Calling Runtime
- 多代理协作系统
- 面向开发者工作流的 AI 产品
- 基于权限控制的本地执行代理
- Skills / Plugins / MCP 扩展体系

---

## What's In This Repo
## 当前仓库内容

This repository mainly contains Claude Code's `src/` directory.

- Language: TypeScript
- Runtime: Bun
- UI layer: React + Ink
- Scale: large production codebase, better read module-by-module than linearly

当前仓库主要包含 Claude Code 的 `src/` 目录。

- 语言：TypeScript
- 运行时：Bun
- 交互层：React + Ink
- 代码规模：大体量工程，适合按模块拆读，不适合从头线性通读

---

## Recommended Reading Order
## 推荐阅读顺序

Do not scan everything at once. For a codebase of this size, the right approach is to follow the trunk first, then branch outward.

### 1. Start with the program entry
### 1. 先看程序入口

- `src/main.tsx`
- `src/entrypoints/cli.tsx`
- `src/commands.ts`

This layer answers:

- how the CLI starts
- where argument parsing and command dispatch happen
- where the React/Ink tree gets mounted
- how different run modes split during startup

这一层回答的是：

- CLI 是怎么启动的
- 参数解析与命令分发在哪里做
- React/Ink 渲染树从哪里接起来
- 不同运行模式在启动阶段如何分流

### 2. Move to the execution core
### 2. 再看核心执行引擎

- `src/QueryEngine.ts`
- `src/Tool.ts`
- `src/context.ts`
- `src/cost-tracker.ts`

This is the heart of Claude Code:

- how model requests are organized
- how the tool-call loop runs
- how messages, context, token accounting, and cost are handled
- why the agent can sustain long-running engineering tasks

这一层是 Claude Code 的“心脏”：

- 模型请求如何组织
- tool call loop 如何运行
- 消息流、上下文、token、成本如何管理
- agent 为什么能在一次会话里持续执行复杂任务

### 3. Study the tool system
### 3. 然后看工具系统

- `src/tools/`
- `src/constants/tools.ts`
- `src/hooks/toolPermission/` if present

This is one of the most important areas because Claude Code is built around toolized execution, not plain chat.

You can see how it turns capabilities into independent tools:

- shell execution
- file read / write / edit
- search and retrieval
- web capabilities
- agent / team / task operations
- MCP
- LSP
- notebook editing

这里最值得学习，因为 Claude Code 的核心抽象不是“聊天”，而是“工具化执行”。

你会看到它如何把能力拆成独立工具：

- Shell 执行
- 文件读写 / 编辑
- 搜索与检索
- Web 能力
- Agent / Team / Task
- MCP 调用
- LSP 集成
- Notebook 编辑

### 4. Then read commands and interaction
### 4. 再看命令与交互层

- `src/commands/`
- `src/components/`
- `src/screens/`
- `src/hooks/`

This layer answers:

- how slash commands are implemented
- how complex terminal interactions are made maintainable
- how dialogs, reviews, diffs, resume flows, and diagnostics compose together

这一层回答的是：

- slash commands 如何实现
- 终端里的复杂交互如何做成可维护组件
- Dialog、Review、Diff、Resume、Doctor 等能力如何组合

### 5. Finish with platform extensions
### 5. 最后看扩展能力

- `src/bridge/`
- `src/services/`
- `src/plugins/`
- `src/skills/`
- `src/coordinator/`
- `src/remote/`
- `src/server/`

This is where Claude Code reveals itself as more than a local CLI. It is a platform that can connect to IDEs, external systems, and multi-agent workflows.

这部分体现 Claude Code 不只是单机 CLI，而是一个可以连接 IDE、扩展外部系统、组织多代理协作的运行平台。

---

## Must-Read Files
## 必读文件

If you only have limited time, start here.

如果你的时间有限，优先看这些。

### Tier 1: Core control flow
### 第一层：主控制链路

- `src/main.tsx`
- `src/entrypoints/cli.tsx`
- `src/commands.ts`
- `src/QueryEngine.ts`
- `src/Tool.ts`

### Tier 2: Execution and context
### 第二层：执行与上下文

- `src/context.ts`
- `src/cost-tracker.ts`
- `src/Task.ts`
- `src/history.ts`
- `src/constants/tools.ts`
- `src/constants/systemPromptSections.ts`

### Tier 3: Product surface
### 第三层：产品交互面

- `src/components/App.tsx`
- `src/components/Messages.tsx`
- `src/components/MessageResponse.tsx`
- `src/components/ToolUseLoader.tsx`
- `src/components/StructuredDiff.tsx`
- `src/components/StatusLine.tsx`

### Tier 4: Platform capability
### 第四层：平台能力

- `src/bridge/bridgeMain.ts`
- `src/bridge/replBridge.ts`
- `src/bridge/sessionRunner.ts`
- `src/commands/review.ts`
- `src/commands/commit.ts`
- `src/commands/install.tsx`

### Tier 5: Architecture edge cases worth learning from
### 第五层：值得研究的边界能力

- `src/commands/security-review.ts`
- `src/components/BypassPermissionsModeDialog.tsx`
- `src/components/MCPServerApprovalDialog.tsx`
- `src/components/ResumeTask.tsx`
- `src/buddy/prompt.ts`
- `src/coordinator/coordinatorMode.ts`

---

## Modules Worth Studying
## 值得重点研究的模块

### `src/QueryEngine.ts`

One of the most important files in the entire codebase.

Focus on:

- streaming response handling
- tool-use loops
- thinking / reasoning related control flow
- retries and recovery
- token and cost accounting
- session state progression

这是最值得精读的文件之一。这里通常能看出 Claude Code 的真实工程能力，而不只是 UI 层面的包装。

重点关注：

- streaming response 处理
- tool use 循环
- thinking / reasoning 相关流程
- retry 与错误恢复
- token 和成本统计
- 会话状态推进方式

### `src/Tool.ts`

If you want to recreate Claude Code's core pattern, this file matters a lot.

Focus on:

- unified tool interface
- input schemas and typing
- permission declarations
- progress and result shapes
- consistency across different tool types

Claude Code's key design move is to abstract capabilities into tools first, then let the model orchestrate them inside controlled boundaries.

如果你想复刻 Claude Code 的核心范式，这个文件非常关键。

重点关注：

- tool 的统一接口
- 输入 schema 与类型约束
- 权限声明
- 进度状态与结果结构
- 不同工具的一致性抽象

Claude Code 的一个核心设计理念，就是先把“能力”抽象成工具，再让模型在受控环境里调度这些工具。

### `src/commands.ts`

Useful for understanding:

- command registration
- how multiple command modes reuse infrastructure
- how a growing CLI remains structurally manageable

这个文件适合用来理解：

- 命令注册机制
- 不同命令模式如何复用基础设施
- CLI 产品如何在大规模功能扩张后仍维持可管理结构

### `src/bridge/`

Important if you care about IDE integration.

Look at:

- bidirectional CLI/IDE communication
- session bridging
- permission callback routing
- bridge protocol and state sync

如果你关心 IDE 集成，这部分很重要。

重点看：

- CLI 与 IDE 如何双向通信
- session 如何桥接
- 权限确认如何传递
- bridge 协议与状态同步如何做

### `src/components/`

This area is easy to underestimate. A large part of Claude Code's terminal quality comes from here.

Pay attention to:

- how React + Ink is organized for a complex CLI
- how message flow, status hints, diffs, and approval dialogs are rendered
- how terminal UX approaches desktop-grade interactions

很多人低估这部分，但 Claude Code 的终端体验质量，很大程度来自这里。

你可以重点观察：

- React + Ink 在复杂 CLI 中的组织方式
- 消息流 UI、状态提示、diff 展示、审批弹窗如何实现
- 终端应用如何做接近桌面应用的交互体验

### `src/services/`

This layer shows how product capability is decomposed into service boundaries:

- API client
- MCP management
- OAuth
- LSP
- analytics / feature flags
- memory / compression / managed settings

This is one of the main boundaries between business logic and infrastructure integration.

这里能看出 Claude Code 如何把产品能力拆到服务层，包括：

- API client
- MCP 管理
- OAuth
- LSP
- analytics / feature flags
- memory / compression / managed settings

这是“业务逻辑”和“基础设施接入层”的主要边界。

---

## Core Design Ideas
## Claude Code 的核心设计理念

### 1. It is an execution agent, not just a chat tool
### 1. 不是聊天工具，而是执行代理

Claude Code is structured around task execution rather than conversation alone.

It behaves more like:

- an agent runtime with UI
- a tool orchestrator
- an execution environment for engineering workflows

instead of a traditional question-answer CLI.

Claude Code 的主体结构不是围绕聊天窗口设计的，而是围绕“任务执行”设计的。

它更像：

- 一个带 UI 的 agent runtime
- 一个工具编排器
- 一个面向开发工作流的执行环境

而不是传统意义上的问答型 CLI。

### 2. Tools first, prompts second
### 2. 工具优先，而不是 Prompt 优先

Many AI projects push complexity into prompts. Claude Code is more engineering-oriented:

- define tool boundaries first
- define permission boundaries second
- let the model make decisions inside those boundaries last

That is more controllable and more robust than prompt-only systems.

很多 AI 项目把能力堆在 prompt 上。Claude Code 更强的地方在于：

- 先定义工具边界
- 再定义权限边界
- 最后才让模型在边界内决策

这比单纯依赖 prompt 更工程化，也更可控。

### 3. Permission control is part of the architecture, not an add-on
### 3. 权限系统不是附属功能，而是主架构的一部分

Claude Code does not look like a system where permissions were added later. Permission checks appear to sit directly on the critical path.

That implies a design stance:

- let the model act
- but only through structured, auditable, approvable, rejectable operations

This is central to any serious local execution agent.

Claude Code 并不是“先把能力做出来，再补权限”。从源码结构看，权限检查明显是核心路径的一部分。

这意味着它的设计思路是：

- 默认允许模型做事
- 但每类能力都必须先被结构化、可审计、可批准、可拒绝

这是本地执行代理产品非常关键的一点。

### 4. The CLI is a real interaction layer
### 4. CLI 不是简陋前端，而是完整交互层

Claude Code treats the terminal as a serious product surface:

- status notices
- approval flows
- diff views
- search
- session resume
- mode switches
- task and multi-agent status displays

This is not "a web app forced into a terminal". It is a terminal-native interaction model.

Claude Code 没把终端当作“凑合能用的入口”，而是认真构建了一整套交互界面：

- 状态提示
- 审批流
- Diff 视图
- 搜索
- 恢复会话
- 模式切换
- 任务与多代理状态展示

这说明它的产品思路不是“把网页搬进终端”，而是为终端环境单独设计交互模型。

### 5. It is a platform, not a single feature
### 5. 它本质上是可扩展平台

The presence of `skills`, `plugins`, `MCP`, `bridge`, `remote`, and `server` shows that Claude Code is aiming at extensibility:

- connectable
- composable
- orchestratable
- portable across environments

That is one of the biggest differences between Claude Code and simple one-file agent demos.

从 `skills`、`plugins`、`MCP`、`bridge`、`remote`、`server` 这些目录可以看出，Claude Code 的目标并不只是单点功能，而是平台化：

- 可扩展
- 可连接
- 可编排
- 可迁移到不同运行环境

这也是它对很多“单文件 agent demo”的本质优势。

---

## Forking Checklist
## 二开清单

If you want to build your own version inspired by Claude Code, do not try to clone the whole thing at once. Start with a smaller, stable core.

如果你想做一个基于 Claude Code 设计思路的二开版本，不要试图一次性照搬全仓，先抽最小稳定骨架。

### Phase 1: minimum useful skeleton
### 第一阶段：最小可用骨架

- CLI entrypoint
- session loop
- tool abstraction
- 4 base tools: shell, read, edit, grep
- simple permission confirmation
- basic conversation history

- CLI 入口
- 会话循环
- Tool 抽象
- Bash / Read / Edit / Grep 四个基础工具
- 简单权限确认
- 基础会话历史

### Phase 2: usable product layer
### 第二阶段：可用产品层

- React + Ink UI
- diff rendering
- slash commands
- resume / session restore
- cost tracking
- better error surfacing

- React + Ink 终端 UI
- Diff 展示
- Slash commands
- Resume / Session restore
- 成本统计
- 更清晰的错误反馈

### Phase 3: platform extensions
### 第三阶段：平台扩展层

- skills / plugin model
- MCP integration
- IDE bridge
- multi-agent coordination
- remote / server mode
- memory extraction and sync

- Skills / Plugin 模型
- MCP 集成
- IDE bridge
- 多代理协作
- Remote / server 模式
- 记忆提取与同步

### What to copy as concepts, not literally
### 更适合提炼思路，而不是直接照抄的部分

- tool interfaces
- permission flow shape
- execution loop boundaries
- UI state composition
- command registration patterns
- bridge protocol design

- Tool 接口抽象
- 权限流转结构
- 执行循环边界
- UI 状态组合方式
- 命令注册模式
- Bridge 协议设计

### What to simplify aggressively in your own fork
### 你在二开时应该大胆简化的部分

- feature flags
- product experiments
- remote-managed settings
- analytics and telemetry
- organization-specific policy layers
- product onboarding surfaces

- Feature flags
- 产品实验开关
- 远程托管配置
- analytics / telemetry
- 组织级策略层
- 产品 onboarding 界面

---

## How To Study This Codebase
## 建议的源码学习方法

### Method 1: follow the main path
### 方法一：按主链路读

Recommended path:

`main.tsx -> commands.ts -> QueryEngine.ts -> Tool.ts -> tools/*`

Good for quickly understanding the primary control flow.

推荐路径：

`main.tsx -> commands.ts -> QueryEngine.ts -> Tool.ts -> tools/*`

适合想快速抓住 Claude Code 主体结构的人。

### Method 2: study by capability area
### 方法二：按能力域读

Pick one direction:

- CLI: `commands/`, `components/`, `screens/`
- agent runtime: `QueryEngine.ts`, `Tool.ts`, `tools/`
- IDE integration: `bridge/`
- extension platform: `plugins/`, `skills/`, `services/mcp/`
- multi-agent work: `coordinator/`, `tasks/`, `remote/`

你可以只挑一个方向深挖：

- 做 CLI：看 `commands/`、`components/`、`screens/`
- 做 agent：看 `QueryEngine.ts`、`Tool.ts`、`tools/`
- 做 IDE 集成：看 `bridge/`
- 做扩展平台：看 `plugins/`、`skills/`、`services/mcp/`
- 做多代理：看 `coordinator/`、`tasks/`、`remote/`

### Method 3: study around design questions
### 方法三：按设计问题读

Read with concrete questions:

- how are tool schemas defined?
- how is tool permission approval handled?
- how are diffs and approvals shown in the terminal?
- how is multi-agent state represented?
- how is MCP integrated?
- why React + Ink instead of plain terminal output?

带着问题读源码，效率最高。

例如：

- Claude Code 如何定义 tool schema？
- 如何处理工具权限审批？
- 如何在 CLI 中展示 diff 和审批流程？
- 多代理状态如何呈现？
- MCP 是如何接入的？
- 为什么它用 React + Ink，而不是传统命令行输出？

---

## Questions Worth Taking Notes On
## 适合重点做笔记的问题

1. What is Claude Code's minimal closed loop?
2. Which modules are product features, and which are platform primitives?
3. Is the tool abstraction stable enough to reuse in your own agent?
4. Is permission control embedded in tools or separated as a policy layer?
5. Is the boundary between query engine and UI clean?
6. Which directories reveal future extensibility rather than just current features?
7. If you built a simplified version, which modules would you keep first?

1. Claude Code 的最小核心闭环是什么？
2. 哪些模块是“产品能力”，哪些模块是“平台能力”？
3. tool abstraction 是否足够稳定，是否适合复用到自己的项目？
4. 权限系统是嵌在工具层，还是独立的 policy layer？
5. Query engine 与 UI 的边界是否清晰？
6. 哪些目录体现了“未来扩展”而不只是“当前功能”？
7. 如果自己实现一个简化版 Claude Code，第一版应该保留哪些模块？

---

## Practical Conclusion
## 一个更务实的结论

The most valuable thing to learn from Claude Code is not whether it can call a particular model. It is that it turns all of these into one coherent system:

- model access
- tool execution
- permission approval
- terminal interaction
- session management
- multi-agent collaboration
- external extensibility

If you are building your own coding agent, this repository is valuable not as an object of hype, but as material to dissect, learn from, and distill into your own engineering framework.

Claude Code 最值得学习的，不是它“会不会调某个模型”，而是它把下面这些东西做成了一个统一系统：

- 模型调用
- 工具调用
- 权限审批
- 终端交互
- 会话管理
- 多代理协作
- 外部扩展

如果你正在做自己的 coding agent，这份源码最有价值的部分，不是拿来崇拜，也不是拿来猎奇，而是拿来拆、拿来学、拿来提炼自己的工程框架。

---

## Disclaimer
## 免责声明

This README is for source study, architecture analysis, and engineering research.

It does not provide instructions for bypassing platform security mechanisms, organization policies, permission controls, account restrictions, or other official safeguards.

The highest-value takeaway is not "how to get around controls", but why Claude Code is designed this way, and which of those design patterns you can reuse in your own agent systems.

本仓库 README 的目标是帮助读者进行源码学习、架构分析与工程研究。

不提供绕过平台安全机制、组织策略、权限控制、账号限制或其他官方限制的操作说明。

如果你真的想从这里获得最大价值，最好的方式不是研究“怎么绕”，而是研究 Claude Code 为什么会这样设计，以及哪些设计可以迁移到你自己的 agent 系统里。

# claude-code-learn
