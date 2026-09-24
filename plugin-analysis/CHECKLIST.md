# Claude Plugin Analysis Checklist

## A. Identity & Scope
- [ ] Primary goal stated as user outcome
- [ ] Success conditions identified
- [ ] Non-goals identified
- [ ] Plugin version/source recorded

## B. Source Inventory
- [ ] plugin.json
- [ ] marketplace.json
- [ ] commands
- [ ] skills
- [ ] agents
- [ ] hooks
- [ ] scripts
- [ ] MCP
- [ ] settings/LSP
- [ ] docs/templates/references
- [ ] unused/legacy candidates marked

## C. Entry Points
- [ ] Slash commands
- [ ] Natural-language activation
- [ ] Explicit skill invocation
- [ ] Hooks/lifecycle events
- [ ] Preconditions
- [ ] Arguments
- [ ] Next step

## D. Workflow
- [ ] Main actor identified
- [ ] Nodes enumerated
- [ ] Edges enumerated
- [ ] Conditions
- [ ] Parallel fan-out
- [ ] Join/aggregation
- [ ] Loops
- [ ] Retries
- [ ] Fallbacks
- [ ] Early exits
- [ ] Human approval gates
- [ ] Termination guards

## E. Components
- [ ] Commands classified
- [ ] Skills classified
- [ ] Agent contracts extracted
- [ ] Agent call graph built
- [ ] Tool operations semanticized
- [ ] MCP servers analyzed
- [ ] Scripts analyzed
- [ ] Hooks analyzed

## F. Context & State
- [ ] Global context
- [ ] User context
- [ ] Runtime context
- [ ] Agent-specific context
- [ ] Context propagation
- [ ] Ephemeral state
- [ ] Session state
- [ ] Persistent state
- [ ] Cache/invalidation

## G. Risk & Boundaries
- [ ] Filesystem reads
- [ ] Filesystem writes
- [ ] Shell execution
- [ ] Git mutation
- [ ] Network access
- [ ] External mutations
- [ ] Credential/env requirements
- [ ] User approvals
- [ ] Plugin/runtime responsibility split

## H. Failure & Output
- [ ] Tool failure
- [ ] Agent failure
- [ ] MCP failure
- [ ] Script failure
- [ ] Missing dependency
- [ ] Partial completion
- [ ] Recovery strategy
- [ ] Output contract
- [ ] Verification layer

## I. Evidence Discipline
- [ ] All important claims have evidence
- [ ] Inference separated from observation
- [ ] Unknowns remain unknown
- [ ] Paths/line ranges recorded
- [ ] Runtime events referenced
- [ ] No secrets captured

## J. Runtime Validation
- [ ] Representative scenario executed
- [ ] Runtime trace captured
- [ ] Static expected vs actual compared
- [ ] Unused declared components identified
- [ ] Unexpected behavior identified

## K. Migration Readiness
- [ ] Portable semantics identified
- [ ] Claude-specific dependencies identified
- [ ] External dependencies identified
- [ ] Unresolved migration blockers listed
