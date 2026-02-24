---
name: create-pr
description: Opens a pull request against `main` for the current feature branch as part of trunk based development.
---

Your task:

1. Confirm we are on a feature branch, NOT `main`.
   - If on `main` or in detached HEAD, STOP and instruct the user to create or switch to a feature branch before proceeding.

2. Use the most recent commit message as the PR title and body:
   - Title: `{{FEATURE-ID}}` the commit message header.
   - Body: the commit message body.

3. Open a pull request targeting `main` using `gh pr create`:
   - `--base` `main`
   - Print the PR URL on completion.
