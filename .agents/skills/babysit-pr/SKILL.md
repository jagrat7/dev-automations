---
name: babysit-pr
description: Monitor a PR, fix critical review comments, and ask the user about comments that require a judgment call.
---

# Babysit PR

Monitor the current PR, or the PR the user names.

On each pass:

1. Read new review comments and check failures.
2. Fix issues that are clearly critical and run the relevant checks.
3. If a comment is debatable ask the user.
4. Continue monitoring until the user stops you or the review agents are satisfied with the fixes


