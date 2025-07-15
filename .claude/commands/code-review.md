---
description: Perform comprehensive code review for merge request
allowed-tools: Bash, Read, Grep, mcp__context7
---

# Code Review for Merge Request

Analyze committed code changes in the current branch for merge request review.

## Instructions:
1. Get the current branch name: !`git branch --show-current`
2. Find the parent branch (the branch from which current branch was created):
   - First check merge-base with common branches: !`git merge-base --fork-point main HEAD 2>/dev/null || git merge-base --fork-point master HEAD 2>/dev/null || git merge-base --fork-point develop HEAD 2>/dev/null || echo ""`
   - Get the actual parent branch: !`git log --oneline --boundary --pretty=format:"%h %d" | grep -v "^$(git rev-parse --short HEAD)" | head -20`
   - If parent branch is unclear, use: !`git reflog show --no-abbrev $(git branch --show-current) | grep "branch: Created from" | head -1`
3. List all commits in the current branch that are not in the parent branch: !`git log --oneline HEAD --not $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))`
4. Show the full diff of committed changes: !`git diff $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`
5. Get file statistics for changed files: !`git diff --stat $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`

## Perform comprehensive code review covering:
- **Code quality and best practices**
- **Potential bugs and issues**
- **Security vulnerabilities**
- **Performance implications**
- **Test coverage and testability**
- **Code maintainability**
- **Compliance with coding standards**

## For each issue found:
- **Always provide specific fix recommendations** with code examples
- **Use MCP context7** to search for best practices, documentation, and optimal solutions for the technology stack
- Reference official documentation and industry standards
- Suggest alternative approaches when applicable
- Include "before" and "after" code examples

## Focus areas for QA Technical Lead perspective:
- Testing strategy and coverage
- Risk assessment for the changes
- Integration points and potential conflicts
- Error handling and edge cases
- Data validation and sanitization
- API contracts and backward compatibility
- Performance impact on critical paths

## Research requirements:
Before making recommendations, use context7 to find:
- Official documentation for used libraries/frameworks
- Best practices for identified patterns
- Security guidelines for found vulnerabilities
- Testing approaches for the technology stack
- Performance optimization techniques
- Industry standards for code quality

## Output format:
### 1. Executive Summary
- Branch: [current branch] → [parent branch]
- Total commits reviewed: [number]
- Files changed: [number]
- Lines added/removed: [+X/-Y]
- Overall risk level: [Low/Medium/High/Critical]

### 2. Detailed Findings by Category
#### Code Quality Issues
- Issue description
- Location: [file:line]
- Current code: ```language ... ```
- Recommended fix: ```language ... ```
- Reference: [context7 documentation link]

#### Security Vulnerabilities
[Same format as above]

#### Performance Concerns
[Same format as above]

### 3. Testing Recommendations
- Suggested test cases
- Coverage improvements
- Integration test scenarios

### 4. Action Items
- Critical (must fix before merge)
- Important (should fix)
- Nice to have (can be addressed later)

### 5. Positive Highlights
- Well-implemented features
- Good practices observed

## Additional context from user: $ARGUMENTS