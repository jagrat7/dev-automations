# OpenCode CLI

Tested on 2026-09-10 with OpenCode `1.18.29`.

Check before use:

```bash
opencode --version
opencode run --help
```

Confirm the model and reasoning variant before launch:

```bash
opencode models
```

Model names use `provider/model`. Reasoning is a model-specific variant, so do not assume every model supports values such as `high` or `max`.

Put the assignment in a temporary file. The installed `build` agent is the implementation-capable primary agent:

```bash
opencode run \
  --model "$worker_model" \
  --variant "$worker_reasoning" \
  --agent build \
  --dir "$worker_repo" \
  --auto \
  --format json \
  --file "$task_file" \
  'Implement the task in the attached file and verify the result.'
```

Read the JSON events and keep the session ID. Resume that session for corrections:

```bash
opencode run \
  --session "$session_id" \
  --model "$worker_model" \
  --variant "$worker_reasoning" \
  --agent build \
  --dir "$worker_repo" \
  --auto \
  --format json \
  --file "$follow_up_file" \
  'Apply the correction in the attached file and verify the result.'
```

Use `opencode session list --format json` if the session ID is not clear in the run output.

Source: [OpenCode CLI reference](https://dev.opencode.ai/docs/cli/#run)
