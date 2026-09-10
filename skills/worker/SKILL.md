---
name: worker
description: Run an implementation task in a Codex, Claude, OpenCode, or Devin CLI subagent using the requested model and reasoning level.
disable-model-invocation: true
---

# Worker

Take these inputs:

- `agent`: `codex`, `claude`, `opencode`, or `devin`
- `model`
- `reasoning`
- `task`

Before starting the subagent, read only its reference:

- Codex: [references/codex.md](references/codex.md)
- Claude: [references/claude.md](references/claude.md)
- OpenCode: [references/opencode.md](references/opencode.md)
- Devin: [references/devin.md](references/devin.md)

If a reference no longer matches the installed CLI, tell the user.

Start the selected CLI as a child process with the requested model and reasoning level. That CLI process is the implementation subagent. 

Give it the task and acceptance criteria. Include decisions from the conversation that are not recorded in the repository.

Run the subagent in the CLI's unrestricted mode. The parent is responsible for giving it the right task and direction.

The subagent owns implementation. Monitor it until it finishes. Review its result and resume the same CLI session with follow-up instructions if the work does meet your expectations. The parent's jobs is to provide taste and knowledge the worker might lack.

Do not implement the task in the parent thread. Do not silently change the requested agent, model, or reasoning level. If the requested combination cannot be spawned, tell the user what is unsupported and ask for a replacement.
