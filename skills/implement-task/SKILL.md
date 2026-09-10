---
name: implement-task
description: Implement a task in a subagent using the requested coding agent, model, and reasoning level.
disable-model-invocation: true
---

# Implement task

Take these inputs:

- `agent`: `codex`, `opencode`, or `claude`
- `model`
- `reasoning`
- `task`

Spawn an implementation subagent with the requested agent, model, and reasoning level. Give it the task, relevant repository context, and acceptance criteria.

The subagent owns implementation and verification. Monitor it until it finishes. Review its result and send follow-up instructions to the same subagent if the work is incomplete.

Do not implement the task in the parent thread. Do not silently change the requested agent, model, or reasoning level. If the requested combination cannot be spawned, tell the user what is unsupported and ask for a replacement.
