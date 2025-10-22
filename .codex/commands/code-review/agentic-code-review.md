---
description: Orchestrates multiple specialized sub-agents to perform comprehensive parallel code review
---

# Agentic Code Review Orchestrator

Performs comprehensive code review by orchestrating multiple specialized sub-agents working in parallel.

## Instructions:

1. First, gather git context for all sub-agents:
   - Current branch: !`git branch --show-current`
   - Parent branch detection: !`git merge-base --fork-point main HEAD 2>/dev/null || git merge-base --fork-point master HEAD 2>/dev/null || git merge-base --fork-point develop HEAD 2>/dev/null || echo ""`
   - List commits: !`git log --oneline HEAD --not $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))`
   - Get full diff: !`git diff $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`
   - File statistics: !`git diff --stat $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`

2. Launch all sub-agents in parallel with the gathered context:

Task(
  description="Code quality review",
  subagent_type="code-quality-reviewer",
  prompt=f"""Review code quality for the following changes:
  
  Branch: {current_branch} → {parent_branch}
  Commits: {commits_list}
  
  DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY issue found, you MUST provide:
  1. The problematic code snippet
  2. The complete fixed/improved code snippet
  3. Clear explanation of the fix
  
  Use this format for each issue:
  **Issue**: [description]
  **Location**: [file:line]
  
  **Current code**:
  ```language
  [exact problematic code]
  ```
  
  **Fixed code**:
  ```language
  [complete working solution]
  ```
  
  **Explanation**: [why this fix solves the problem]
  """
)

Task(
  description="Security analysis",
  subagent_type="security-reviewer", 
  prompt=f"""Analyze security vulnerabilities in the following changes:
  
  Branch: {current_branch} → {parent_branch}
  Commits: {commits_list}
  
  DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY vulnerability found, you MUST provide:
  1. The vulnerable code snippet
  2. The complete secure implementation
  3. Clear explanation of the fix
  
  Use this format for each vulnerability:
  **Vulnerability**: [type and description]
  **Location**: [file:line]
  **Severity**: [Critical/High/Medium/Low]
  
  **Vulnerable code**:
  ```language
  [exact vulnerable code]
  ```
  
  **Secure code**:
  ```language
  [complete secure solution]
  ```
  
  **Explanation**: [how this prevents the vulnerability]
  """
)

Task(
  description="Performance review",
  subagent_type="performance-reviewer",
  prompt=f"""Review performance implications of the following changes:
  
  Branch: {current_branch} → {parent_branch}
  Commits: {commits_list}
  
  DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY performance issue found, you MUST provide:
  1. The slow/inefficient code snippet
  2. The complete optimized implementation
  3. Clear explanation of the improvement
  
  Use this format for each issue:
  **Issue**: [performance problem]
  **Location**: [file:line]
  **Impact**: [High/Medium/Low]
  
  **Current implementation**:
  ```language
  [exact slow code]
  ```
  
  **Optimized code**:
  ```language
  [complete optimized solution]
  ```
  
  **Performance gain**: [estimated improvement]
  **Explanation**: [why this is faster]
  """
)

Task(
  description="Testing assessment",
  subagent_type="testing-reviewer",
  prompt=f"""Assess testing coverage and quality for the following changes:
  
  Branch: {current_branch} → {parent_branch}
  Commits: {commits_list}
  
  DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY testing gap found, you MUST provide:
  1. The untested code
  2. Complete test implementation
  3. Clear explanation of test coverage
  
  Use this format for each gap:
  **Missing Test**: [what needs testing]
  **Location**: [file/function needing tests]
  **Priority**: [Critical/High/Medium/Low]
  
  **Code to test**:
  ```language
  [exact code needing tests]
  ```
  
  **Test implementation**:
  ```language
  [complete test code]
  ```
  
  **Test scenarios covered**:
  - [scenario 1]
  - [scenario 2]
  - [edge cases]
  """
)

Task(
  description="Bug detection",
  subagent_type="bug-detector",
  prompt=f"""Detect potential bugs and edge cases in the following changes:
  
  Branch: {current_branch} → {parent_branch}
  Commits: {commits_list}
  
  DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY bug found, you MUST provide:
  1. The buggy code snippet
  2. The complete fixed implementation
  3. Clear explanation of the bug and fix
  
  Use this format for each bug:
  **Bug Type**: [category and description]
  **Location**: [file:line]
  **Severity**: [Critical/High/Medium/Low]
  
  **Problematic code**:
  ```language
  [exact buggy code]
  ```
  
  **Fixed code**:
  ```language
  [complete corrected code]
  ```
  
  **Scenario to trigger**: [how to reproduce]
  **Explanation**: [why the fix prevents the bug]
  """
)

3. Aggregate results from all sub-agents and present a unified report:

## Final Report Format:

### Executive Summary
- Branch: [current] → [parent]
- Total commits: [number]
- Files changed: [number]
- Lines: [+added/-removed]
- Overall risk: [aggregated from all agents]

### Findings by Category

#### 🎨 Code Quality (from code-quality-reviewer)
[Agent's findings]

#### 🔒 Security (from security-reviewer)
[Agent's findings]

#### ⚡ Performance (from performance-reviewer)
[Agent's findings]

#### 🧪 Testing (from testing-reviewer)
[Agent's findings]

#### 🐛 Potential Bugs (from bug-detector)
[Agent's findings]

### Consolidated Action Items

#### Critical (must fix before merge)
[For each critical issue, include the code fix example from the relevant agent]

#### Important (should fix)  
[For each important issue, include the code fix example from the relevant agent]

#### Nice to have (can be addressed later)
[For each nice-to-have issue, include the code fix example from the relevant agent]

### Code Examples Summary

[Provide a quick reference of the most critical fixes with before/after code snippets]

Example format:
**1. Fix null reference bug (from bug-detector)**
```javascript
// Before:
if (config.use.baseURL) { // config.use might be undefined

// After:
if (config.use?.baseURL) { // Safe navigation
```

**2. Improve URL validation (from security-reviewer)**
```javascript
// Before:
const urlRegex = /^https?:\/\/.+/;

// After:
try {
  new URL(url);
  return true;
} catch {
  return false;
}
```

### Positive Highlights
[Aggregate positive findings from all agents]

## Additional context: $ARGUMENTS