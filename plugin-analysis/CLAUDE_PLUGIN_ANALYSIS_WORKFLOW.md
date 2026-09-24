# Claude Code Plugin 拆机 Workflow

## 0. 分析目标

不要把 plugin 当成目录树，而要还原以下系统：

```text
User Intent
   ↓
Trigger
   ↓
Entry Point
   ↓
Main Agent / Router
   ↓
Workflow / Control Flow
   ↓
Skills / Subagents / Tools / Hooks / MCP / Scripts
   ↓
Context + State
   ↓
Verification / Recovery
   ↓
Output
```

最终分析必须覆盖以下层。

## 1. Identity

记录：
- plugin name / version / author / repository
- primary goal
- secondary goals
- target user
- expected success condition

重点：用“解决什么问题”描述，不要只写“包含哪些组件”。

## 2. Source Inventory

扫描：
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `commands/`
- `skills/`
- `agents/`
- `hooks/`
- `scripts/`
- `references/`
- `templates/`
- `.mcp.json`
- `.lsp.json`
- `settings.json`
- README / docs

每个文件标记：
- purpose
- runtime_required
- referenced_by
- user_visible
- evidence

注意：**存在不等于运行时会使用。**

## 3. Entry Points

枚举所有入口：

### 3.1 Explicit
- slash command
- CLI command
- plugin command
- explicit skill invocation

### 3.2 Implicit
- natural-language skill activation
- description-based routing
- automatic agent delegation

### 3.3 Lifecycle
- SessionStart
- PreToolUse
- PostToolUse
- Stop
- Notification
- 其他 hooks

每个 entrypoint 必须说明：
- trigger
- arguments
- preconditions
- context required
- next node
- failure mode

## 4. Intent Contract

每个入口不要只记录 prompt，要提取：

```yaml
goal:
success_conditions:
non_goals:
preconditions:
side_effect_policy:
```

这是迁移时最重要的 platform-independent contract。

## 5. Main Agent Role

判断主 Agent 属于哪种模式：

1. Executor：自己完成大部分任务。
2. Orchestrator：主要做路由、拆解、委派、聚合。
3. Hybrid：部分自己做，部分交给 subagents / scripts / MCP。

记录：
- responsibilities
- decisions
- allowed tools
- delegation policy
- aggregation policy

## 6. Workflow Graph

把 workflow 建成 DAG / state machine，而不是自然语言段落。

每个 node 应包含：
- id
- actor
- operation
- condition
- inputs
- outputs
- side effects
- next nodes

每条 edge 应包含：
- from
- to
- condition
- edge type: sequential / conditional / parallel / retry / fallback

## 7. Control Flow

必须检查：

- branch / if / switch
- loop
- retry
- fallback
- early exit
- parallel fan-out
- join / aggregation
- human approval gate
- max iteration / termination guard

真正的 workflow intelligence 往往在这里。

## 8. Commands

对每个 command 记录：

- user-facing name
- argument syntax
- command prompt/body
- whether it contains business logic
- delegates_to skill/agent/script
- command-specific context
- slash-command-only semantics

重点判断 command 是：
- UX wrapper
- router
- real workflow owner

## 9. Skills

对每个 skill 分类：

- instruction skill
- workflow skill
- router skill
- knowledge skill
- tool wrapper
- validation skill

记录：
- activation rule
- instructions
- inputs/outputs
- tools
- dependency skills
- context assumptions
- termination

## 10. Subagents

把每个 subagent 当成函数：

```text
Agent(input, context, tools, constraints) -> output
```

记录：
- role
- goal
- model
- prompt / developer instructions
- tools
- forbidden actions
- context scope
- input contract
- output contract
- termination
- caller
- downstream consumer

## 11. Agent Call Graph

必须生成调用关系：

```text
Main
 ├─ Agent A
 │   └─ Agent C
 └─ Agent B
```

同时记录：
- blocking / parallel / background
- parent-child relationship
- fan-out condition
- join condition

## 12. Tool Semantics

不要只记录工具名。

错误：
```text
Bash
Read
Task
```

正确：
```text
inspect_git_diff -> Bash("git diff")
read_repo_policy -> Read("CLAUDE.md")
delegate_security_review -> Task(security-agent)
```

每个 operation 记录：
- semantic purpose
- implementation tool
- arguments
- read/write side effects
- approval requirement
- error behavior

## 13. MCP

每个 MCP server 记录：
- server name
- transport
- startup command
- auth/env requirements
- tools/resources/prompts
- required vs optional
- call sites
- external side effects

## 14. Scripts

scripts 通常承载 deterministic logic。

记录：
- caller
- language/runtime
- input
- output
- filesystem/network side effects
- why script exists instead of LLM reasoning
- failure code semantics

## 15. Hooks

每个 hook 记录：
- event
- matcher/condition
- action
- sync/blocking
- inputs
- output/exit code
- state mutation
- user visibility
- failure policy

尤其检查 hooks 是否承担：
- formatting
- validation
- guardrails
- environment setup
- skill bridge / symlink
- state persistence

## 16. Context Model

拆分 context：

### Global
- CLAUDE.md
- project instructions
- plugin instructions

### User
- current request
- arguments
- attachments

### Runtime
- cwd
- git branch
- git diff
- environment
- tool outputs

### Agent-specific
- selected files
- parent summary
- full conversation or reduced context

必须记录 context 如何传播和裁剪。

## 17. State Model

分类：

- ephemeral task state
- session state
- filesystem state
- cache
- persistent DB/memory
- external service state

记录：
- owner
- reader/writer
- lifetime
- initialization
- invalidation

## 18. Permissions & Side Effects

建立 capability boundary：

- read filesystem
- write filesystem
- execute shell
- mutate git
- network access
- external API mutation
- MCP mutation
- system configuration
- credential access

明确哪些需要用户 approval。

## 19. Failure & Recovery

至少覆盖：
- missing dependency
- invalid input
- tool error
- agent error
- MCP unavailable
- script failure
- partial completion
- validation failure
- timeout / max iteration

Recovery 类型：
- retry
- fallback
- degrade
- ask user
- abort

## 20. Termination

显式记录：
- success condition
- no-op condition
- early stop
- blocked condition
- max retries
- max iterations

避免“agent 自己应该知道什么时候结束”这种隐含假设。

## 21. Output Contract

记录：
- response format
- files created/modified
- structured result schema
- logs/reports
- user-visible vs internal artifacts
- ordering requirements

## 22. Verification

检查 workflow 是否有：
- lint
- tests
- schema validation
- diff review
- self-review
- second-agent verification
- output completeness check

## 23. Platform Dependency

每个 capability 标记：
- `portable`
- `claude_specific`
- `external`
- `unknown`

重点识别：
- Claude-specific tool names
- hooks semantics
- subagent runtime semantics
- slash command semantics
- context injection
- permission runtime

## 24. Plugin vs Runtime Boundary

必须分别写：

### Plugin provides
prompts, workflows, skills, agents, configs, scripts, hook declarations...

### Claude Code runtime provides
model execution, tool runtime, permissions, context management, agent spawning, hook lifecycle...

## 25. Runtime Trace

实际运行至少一个代表性场景。

记录：
- trigger
- each actor
- tool call
- subagent spawn
- inputs/outputs summary
- state changes
- branch choices
- errors/retries
- final result

不要记录 secrets。

## 26. Expected vs Actual

比较：

```text
Static expected workflow
          vs
Runtime observed workflow
```

重点寻找：
- 文件存在但未使用
- agent 定义存在但未调用
- implicit tool calls
- hidden retries
- unexpected context dependencies
- runtime-specific behavior

## 27. Boundary Analysis

最终明确四组结论：

1. Plugin 能做什么。
2. Plugin 明确做不了什么。
3. 依赖 Claude Code runtime 才能做什么。
4. 换到另一个 Agent runtime 时最可能丢失什么。

## 28. Final Deliverables

每次完整拆机至少生成：
- `plugin-ir.yaml`
- `runtime-trace.yaml`
- `analysis-report.md`

并通过 schema 验证。

## 29. 完成判定

如果无法用下面一句话准确描述，说明还没拆清：

> 该 Plugin 通过 **X 入口**触发，由 **Y actor** 按 **Z control flow** 读取 **A context**、维护 **B state**、调用 **C skills / D agents / E tools / F MCP / G scripts/hooks**，经过 **H verification/recovery** 后产生 **I output**；其中 **J** 为 portable semantics，**K** 为 Claude-specific dependency，主要边界是 **L**。
