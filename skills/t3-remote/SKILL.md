---
name: t3-remote
description: Run a task on another machine through T3 Code's supported MCP connection.
---

Use the target machine's `/mcp` endpoint through a supported MCP client and normal OAuth/pairing.

- Confirm the connected machine with `t3_environment_read`. Tools injected into this thread normally address its own server, even when the app shows other connected machines.
- The app connection and MCP authorization are separate. If MCP needs authorization, use its normal sign-in flow; wait for the required user step.
- Discover the target's available tools. Use its thread tools to send the requested work to a remote agent and read back the result. Create a new conversation only when the user requests one.
- If the endpoint is unavailable or the tools cannot perform the task, explain the blocker. Do not extract saved credentials, copy authentication storage, or fall back to internal APIs.
