---
description: Run code quality checks on modified frontend files
allowed-tools: Bash
---

# Frontend Code Quality Check

Run code quality checks (ESLint, TypeScript, Prettier, build) on changed frontend files in the project.

This command will:

1. Check for modified frontend files using git (ts, tsx, js, jsx)
2. Run ESLint for code quality and potential errors
3. Run Prettier formatting check
4. Run TypeScript type checking (tsc --noEmit)
5. Run build to ensure code compiles successfully

Only files that have been modified (staged or unstaged) will be checked.
