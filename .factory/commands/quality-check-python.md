---
description: Run code quality checks on modified Python files
allowed-tools: Bash
---

# Code Quality Check

Run code quality checks (black, isort, ruff, mypy) on changed Python files in the project.

This command will:

1. Check for modified Python files using git
2. Run black formatting check
3. Run isort import sorting check
4. Run ruff linting
5. Run mypy type checking

Only files that have been modified (staged or unstaged) will be checked.
