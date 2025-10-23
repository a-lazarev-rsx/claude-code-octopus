---
description: Perform code review for specific commit(s)
---

# Code Review for Specific Commit(s)

Analyze code changes in specific commit(s) by commit hash(es).

## Parse commit hashes from arguments: $ARGUMENTS

## Instructions:
1. Parse commit hash(es) from: $ARGUMENTS
2. For each commit hash, analyze the changes using appropriate git commands
3. Gather all necessary information about the commit(s) including:
   - Commit metadata (author, date, message)
   - Files changed
   - Actual code changes (diff)
   - Statistics about the changes

## Perform comprehensive code review covering:
- **Code quality and best practices**
- **Potential bugs and issues**
- **Security vulnerabilities**
- **Performance implications**
- **Test coverage and testability**
- **Code maintainability**
- **Compliance with coding standards**
- **Commit message quality and clarity**
- **Atomic nature of the commit** (does it represent a single logical change?)

## For each issue found:
- **Always provide specific fix recommendations** with code examples
- **Use MCP context7** to search for best practices, documentation, and optimal solutions for the technology stack
- Reference official documentation and industry standards
- Suggest alternative approaches when applicable
- Include "before" and "after" code examples

## Focus areas for commit review:
- Is the commit atomic and focused on a single change?
- Does the commit message clearly describe what and why?
- Are there any unrelated changes mixed in?
- Is the change properly tested?
- Does it follow the project's coding standards?
- Are there any temporary or debug code left in?

## Research requirements:
Before making recommendations, use context7 to find:
- Official documentation for used libraries/frameworks
- Best practices for identified patterns
- Security guidelines for found vulnerabilities
- Testing approaches for the technology stack
- Commit message conventions and standards

## Output format:
### 1. Commit Summary
For each reviewed commit:
- Commit hash: [full hash]
- Author: [name <email>]
- Date: [commit date]
- Message: [commit message]
- Files changed: [number]
- Lines added/removed: [+X/-Y]
- Risk level: [Low/Medium/High/Critical]

### 2. Detailed Findings by Commit
#### Commit: [hash] - [first line of message]
##### Code Quality Issues
- Issue description
- Location: [file:line]
- Current code: ```language ... ```
- Recommended fix: ```language ... ```
- Reference: [context7 documentation link]

##### Security Vulnerabilities
[Same format as above]

##### Performance Concerns
[Same format as above]

##### Commit Structure Issues
- Issue with commit atomicity or message
- Recommendation for improvement

### 3. Cross-Commit Analysis (if multiple commits)
- Dependencies between commits
- Logical grouping assessment
- Suggested commit squashing or reordering

### 4. Testing Recommendations
- Test cases needed for the changes
- Coverage gaps identified
- Regression test suggestions

### 5. Action Items
Prioritized by commit:
- Critical (must fix before merge)
- Important (should fix)
- Nice to have (can be addressed later)

### 6. Positive Highlights
- Well-structured commits
- Good practices observed
- Clear and descriptive commit messages

## Special handling:
- If reviewing multiple commits, analyze them both individually and as a group
- Check for logical dependencies between commits
- Identify if commits should be squashed or reordered
- For merge commits, focus on conflict resolution quality