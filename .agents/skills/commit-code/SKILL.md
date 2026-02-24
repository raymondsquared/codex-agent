---
name: commit-code
description: Commits and tests all current, uncommitted work to the current feature branch as part of trunkbased development.
---

Your task:

1. Make sure we are on a feature branch (NOT `main`).

- If we are on `main` or in detached HEAD, STOP and tell me to create/switch to a feature branch.

2. Read all uncommitted changes (staged, unstaged, and untracked files).

3. Run linting and validation for this repository
- If either command fails, fix the issues before proceeding.

4. Generate a Conventional Commit message:

- Header must be imperative and ≤ 100 characters.
- Header format: {{TYPE}}({{SCOPE}}): {{SHORT SUMMARY IN LOWER CASE}}
- Use one of: feat, fix, refactor, perf, docs, test, chore, build, ci, revert
- Body should be concise (3–6 bullet points or short paragraphs).
- Wrap body lines at 80 characters.
- Clearly explain what changed and why.

5. Stage the relevant files and commit them to the current feature branch.
   - Do NOT push.
   - Do NOT commit to `main`.
