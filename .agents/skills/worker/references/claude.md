# Claude Code CLI

Tested on 2026-09-10 with Claude Code `2.1.266`.

Check before use:

```bash
claude --version
claude --help
```

Use print mode for a non-interactive worker. Run it from the target repository and send the assignment through stdin:

```bash
claude --print \
  --model "$worker_model" \
  --effort "$worker_reasoning" \
  --dangerously-skip-permissions \
  --output-format stream-json \
  --verbose \
  < "$task_file"
```

Claude Code `2.1.266` accepts `low`, `medium`, `high`, `xhigh`, and `max` for `--effort`. Preserve the requested value. Keep the session ID from the stream so corrections return to the same worker:

```bash
claude --print \
  --resume "$session_id" \
  --model "$worker_model" \
  --effort "$worker_reasoning" \
  --dangerously-skip-permissions \
  --output-format stream-json \
  --verbose \
  < "$follow_up_file"
```

Source: [Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)
