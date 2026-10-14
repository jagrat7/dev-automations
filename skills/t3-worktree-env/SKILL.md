---
name: t3-worktree-env
description: Make new git worktrees get the main checkout's env files and dependencies automatically.
disable-model-invocation: true
---

Set up this project so new worktrees symlink the main checkout's gitignored env files, which stay real files there, then install dependencies.

- Copy [scripts/setup-worktree.sh](scripts/setup-worktree.sh) into the project's `scripts/` and adapt it, e.g. the install command.
- Run it on worktree creation: a `t3.json` script with `"runOnWorktreeCreate": true` for T3 Code, and a `post-checkout` hook for plain `git worktree add` if the user wants one.
- Note in `AGENTS.md`/`CLAUDE.md` that env files are edited in the main checkout.
- Verify with a throwaway worktree.
