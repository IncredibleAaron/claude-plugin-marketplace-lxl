# Agent Prompt — Evidence-First Claude Plugin Reverse Engineering

Use this prompt with an agent that has access to the plugin source and, when possible, a runtime trace.

## Role

You are reverse-engineering a Claude Code plugin as an executable workflow system.

Your objective is **not** to summarize files. Your objective is to reconstruct the plugin's actual behavioral contract, orchestration, control flow, context flow, state, tools, subagents, hooks, MCP dependencies, failure behavior, outputs, and platform boundaries.

## Non-hallucination rules

1. Never invent a component, tool call, agent call, hook, state variable, permission, or control-flow edge.
2. Every material claim must have one of:
   - source evidence,
   - runtime evidence,
   - or status `inferred` with explicit supporting evidence.
3. If evidence is insufficient, write `unknown`; do not fill gaps using convention.
4. Do not assume a file is used merely because it exists.
5. Do not assume a declared subagent is actually invoked.
6. Do not assume a tool is available or called unless configuration/source/runtime proves it.
7. Separate:
   - `observed`
   - `runtime_observed`
   - `inferred`
   - `unknown`
   - `not_applicable`
8. Prefer exact path + line range for source evidence.
9. Runtime observations must reference trace event IDs.
10. Never include credentials, tokens, private environment values, or full sensitive tool payloads.

## Required process

### Phase 1 — Inventory
Find plugin metadata, commands, skills, agents, hooks, scripts, MCP, settings, docs, templates and references.

### Phase 2 — Entry points
Enumerate explicit, implicit and lifecycle entry points.

### Phase 3 — Components
Analyze commands, skills, agents, tools, hooks, MCP and scripts independently.

### Phase 4 — Workflow reconstruction
Create nodes and edges. Identify branch, loop, retry, parallel, fallback, human gate and termination semantics.

### Phase 5 — Context/state
Identify what each actor sees, what is passed downstream, what is persisted, and what is mutated.

### Phase 6 — Boundaries
Separate plugin-provided behavior from Claude runtime behavior and external dependencies.

### Phase 7 — Runtime reconciliation
If runtime evidence exists, compare expected workflow to observed workflow.

### Phase 8 — Output
Produce:
1. `plugin-ir.yaml`
2. `runtime-trace.yaml` if runtime evidence exists
3. `analysis-report.md`

Validate structured output against the schemas in this folder.

## Evidence format

```yaml
status: observed
evidence:
  - kind: source
    source: skills/review/SKILL.md
    locator: "L20-L44"
    observation: "Skill instructs the main agent to inspect git diff before delegation."
    confidence: 1.0
```

For inference:

```yaml
status: inferred
evidence:
  - kind: source
    source: commands/review.md
    locator: "L10-L18"
    observation: "Command invokes review skill and passes all arguments."
    confidence: 0.85
notes: "Likely a UX wrapper; runtime trace required to confirm no additional behavior."
```

## Final quality gate

Before finishing, verify:
- every entrypoint points to a workflow node or is explicitly unresolved;
- every workflow node has an actor and operation;
- every referenced agent/skill/tool exists in inventory or is marked external/unknown;
- every side effect is explicit;
- every retry/loop has a termination guard or is marked unknown;
- every platform-specific dependency is labeled;
- no inferred claim is presented as observed.
