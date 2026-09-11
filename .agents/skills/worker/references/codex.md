# Codex CLI

Tested on 2026-09-10 with `codex-cli 0.153.4`.

Check before use:

```bash
codex --version
codex exec --help
```

Use `codex exec` for a non-interactive worker. Put the full assignment in a temporary file and run the command from the target repository:

```bash
codex exec \
  --model "$worker_model" \
  --config "model_reasoning_effort=\"$worker_reasoning\"" \
  --dangerously-bypass-approvals-and-sandbox \
  --json \
  --output-last-message "$result_file" \
  - < "$task_file"
```

Keep the process handle and read the JSONL events. Save the session ID so corrections go back to the same worker:

```bash
codex exec resume "$session_id" \
  --model "$worker_model" \
  --config "model_reasoning_effort=\"$worker_reasoning\"" \
  --dangerously-bypass-approvals-and-sandbox \
  --json \
  - < "$follow_up_file"
```

Pass the working directory through the process runner, or use `--cd "$worker_repo"`.

Source: [Codex CLI reference](https://learn.chatgpt.com/docs/developer-commands?surface=cli#codex-exec)
