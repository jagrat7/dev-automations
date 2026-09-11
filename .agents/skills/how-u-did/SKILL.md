---
name: how-u-did
description: Reconstruct how an agent completed prior work, including what acted, which files or links it touched, and why. Use when the user asks how or where something happened or wants an audit trail.
---

# How u did

Explain the work from evidence in the current thread and workspace. This is a read-only reporting task. Do not repeat or modify the original work just to reconstruct it.

Map the execution clearly:

- Identify who performed each action: the parent agent, a native subagent, a CLI child process, or a tool.
- Link every relevant created, modified, or deleted file. Use absolute local file links and add a line number when it points to a specific implementation detail.
- Link external pages, pull requests, issues, commits, and other resources directly.
- For each action, state what changed and why it was necessary.
- Include commands, session IDs, models, or reasoning levels when they help distinguish how the work ran.
- State how the result was verified and mention relevant failures or retries.

Inspect read-only evidence such as the conversation, process output, version-control status and diffs, and file contents when the available context is incomplete. Do not guess. Label inferences and say when logs or evidence are unavailable.

Keep pre-existing changes separate from changes made by the work being explained. Redact credentials, tokens, and sensitive command arguments.

Use a compact action-to-artifact map when several actions occurred. For a small task, plain prose is enough.
