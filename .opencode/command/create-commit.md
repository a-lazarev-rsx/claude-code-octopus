---
description: Create a git commit with all staged changes
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
---

# Git Commit

Create a git commit with the provided message or auto-generated message based on changes.

## Instructions:
1. Check git status
2. If there are unstaged changes, stage them
3. Show what will be committed
4. Create commit with provided message or auto-generate based on changes
5. Show the created commit

## Commit message format:
- Use conventional commits format when possible (feat:, fix:, docs:, etc.)
- Keep first line under 50 characters
- Do not use emojis
- Reference issue numbers if mentioned in arguments
## Note:
The command will stage ALL changes before committing. If you need selective staging, use git add manually first.

To run linters before commit, use your project's lint command separately (e.g., `make lint`, `npm run lint`, `uv run ruff check`).
