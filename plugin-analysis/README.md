# Claude Code Plugin Analysis System

目标：把一个 Claude Code plugin 从“文件集合”还原成“可执行工作流系统”，并用可验证的结构化证据描述它。

## 核心原则

1. **行为优先于文件结构**：重点回答 plugin 如何被触发、如何编排、调用谁、读写什么、何时结束。
2. **证据优先于推断**：任何重要结论必须绑定 evidence；缺少证据时写 `unknown`，禁止补全。
3. **Static 与 Runtime 分离**：
   - Static analysis：从 manifest、commands、skills、agents、hooks、scripts、MCP、settings 等源码推导“可能的设计”。
   - Runtime trace：实际运行 plugin，记录“真实发生的行为”。
4. **平台语义优先于工具名**：记录 operation semantics，例如“读取 git diff”“委派 bounded review task”，不要只记录 `Bash` 或 `Task`。
5. **Plugin logic 与 Claude runtime 分离**：明确哪些能力来自 plugin，哪些能力由 Claude Code runtime 提供。
6. **迁移前先还原语义**：未来做 Codex migration 时，应从 workflow IR 生成 Codex adapter，而不是直接做文件到文件映射。

## 推荐输出

```text
plugin-analysis/
├── README.md
├── CLAUDE_PLUGIN_ANALYSIS_WORKFLOW.md
├── ANALYSIS_PROMPT.md
├── CHECKLIST.md
├── schemas/
│   ├── plugin-ir.schema.json
│   └── runtime-trace.schema.json
└── templates/
    ├── plugin-ir.template.yaml
    ├── runtime-trace.template.yaml
    └── analysis-report.template.md
```

## 标准流程

```text
Plugin Source
   │
   ├── Static inventory
   ├── Entry-point discovery
   ├── Component analysis
   ├── Workflow reconstruction
   ├── Tool / agent / context / state analysis
   └── Boundary analysis
          │
          ▼
     plugin-ir.yaml
          │
          ├── Runtime execution
          ▼
 runtime-trace.yaml
          │
          ▼
 Expected vs Actual
          │
          ▼
 analysis-report.md
```

## 结构化文件

- `plugin-ir.schema.json`：平台无关的 workflow IR schema。
- `plugin-ir.template.yaml`：人工或 Agent 填写模板。
- `runtime-trace.schema.json`：真实执行 trace schema。
- `runtime-trace.template.yaml`：实际运行时记录模板。

JSON Schema 可用于验证 JSON；YAML 解析成对象后也可以用同一个 schema 验证。

## Evidence Policy

每个关键 claim 尽量包含：

```yaml
status: observed
evidence:
  - kind: source
    source: commands/review.md
    locator: "L12-L37"
    observation: "Command explicitly invokes review skill"
    confidence: 1.0
```

状态定义：

- `observed`：源码明确写出。
- `runtime_observed`：运行 trace 明确发生。
- `inferred`：根据证据推断，但源码/trace 没直接声明。
- `unknown`：没有足够证据。
- `not_applicable`：该维度不适用。

禁止把 `inferred` 写成 `observed`。

## 最终判定标准

当分析完成后，应该可以准确回答：

> 该 Plugin 通过什么入口触发，主 Agent 如何编排，读取哪些 context，在什么条件下调用哪些 skills / subagents / tools / MCP / scripts，如何维护 state，如何处理失败，如何终止，产生什么 output；哪些逻辑可移植，哪些依赖 Claude Code runtime，以及它明确做不到什么。
