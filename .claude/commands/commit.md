---
description: Create a git commit with all staged changes
allowed-tools: Bash
---

# Git Commit

Create a git commit with the provided message or auto-generated message based on changes.

## Instructions:
1. Check git status: !`git status --porcelain`
2. Detect project type and run appropriate linters:
   - If Makefile exists with lint targets: !`make lint-all 2>/dev/null || make lint 2>/dev/null`
   - For Python projects: !`uv run ruff check . && uv run mypy . 2>/dev/null || (ruff check . && mypy . 2>/dev/null) || echo "Linters not configured"`
   - For TypeScript/JavaScript: !`npm run lint 2>/dev/null || yarn lint 2>/dev/null || echo "Linters not configured"`
3. If linting fails, stop and ask user to fix issues first
4. If there are unstaged changes, stage them: !`git add -A`
5. Show what will be committed: !`git diff --cached --stat`
6. Create commit with message from arguments or auto-generate based on changes
7. If no message provided, analyze changes and create descriptive commit message
8. Show the created commit: !`git log -1 --oneline`

## Commit message format:
- Use conventional commits format when possible (feat:, fix:, docs:, etc.)
- Keep first line under 50 characters
- Do not use emojis
- Reference issue numbers if mentioned in arguments

## Note:
The command will:
- Run project-specific linters before committing
- Stage ALL changes before committing
- Refuse to commit if linting fails

If you need selective staging, use git add manually first.