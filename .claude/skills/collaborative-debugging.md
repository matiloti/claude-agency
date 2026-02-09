# Collaborative Debugging Skill

## Purpose

Last-resort debugging strategy for stuck agents. Spawns parallel investigation subagents to explore different solution paths.

## When to Use

**Trigger**: After 5 failed attempts to solve the same error/bug.

## Protocol

1. **STOP** making further attempts yourself
2. **Spawn 5 subagents in parallel** (Task tool, `subagent_type: "general-purpose"`):
   - **Subagent 1**: Search GitHub issues for exact error message
   - **Subagent 2**: Search StackOverflow/Reddit for similar symptoms
   - **Subagent 3**: Try alternative library/approach
   - **Subagent 4**: Investigate version/dependency incompatibility
   - **Subagent 5**: Check environmental issues (Docker, config, permissions)
3. **Collect findings** and synthesize the most promising solution
4. **If still unresolved**: Escalate to orchestrator with full context

## Constraints

- Only invoke after **genuine** 5 failed fix attempts
- Do NOT invoke prematurely
- Document all attempted solutions before invoking
