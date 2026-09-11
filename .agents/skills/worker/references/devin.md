# Devin CLI

Tested on 2026-09-10 with Devin `3000.6.14` build `18033302`.

Check before use:

```bash
devin --version
devin --help
```

Devin combines the model and reasoning level into one model UID. Resolve the requested pair from the live catalog instead of constructing a name:

```bash
devin models list --format json
```

Find the requested family by its `slug`, `family_uid`, or alias. Within its `variants`, choose the `model_uid` whose label matches the requested reasoning level. If there is no matching variant, tell the user the combination is unsupported and show the available levels.

Put the assignment in a temporary file and run Devin from the target repository:

```bash
devin \
  --print \
  --model "$resolved_model_uid" \
  --permission-mode dangerous \
  --prompt-file "$task_file"
```

Use `devin list --format json` to recover the session ID when needed. Resume the same worker for corrections:

```bash
devin \
  --print \
  --resume "$session_id" \
  --model "$resolved_model_uid" \
  --permission-mode dangerous \
  --prompt-file "$follow_up_file"
```

Source: [Devin CLI documentation](https://docs.devin.ai/work-with-devin/devin-cli)
