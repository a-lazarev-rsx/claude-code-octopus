---
description: Create a git commit with all staged changes
allowed-tools: Bash
---

# Git Commit

Create a git commit with the provided message or auto-generated message based on changes.

## Instructions:
1. Check git status: !`git status --porcelain`
2. If there are unstaged changes, stage them: !`git add -A`
3. Show what will be committed: !`git diff --cached --stat`
4. Create commit with provided message or auto-generate based on changes
5. Show the created commit: !`git log -1 --oneline`

## Commit message format:
- Use conventional commits format when possible (feat:, fix:, docs:, etc.)
- Keep first line under 50 characters
- Do not use emojis
- Reference issue numbers if mentioned in arguments
## Note:
The command will stage ALL changes before committing. If you need selective staging, use git add manually first.

To run linters before commit, use your project's lint command separately (e.g., `make lint`, `npm run lint`, `uv run ruff check`).
