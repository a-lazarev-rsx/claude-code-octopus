---
description: Perform comprehensive code review for pull request using multiple specialized agents (GitHub/Bitbucket)
allowed-tools: Task, Bash, Read, Grep
argument-hint: PR_NUMBER [target_branch]
---

# Pull Request Review with Agents

Orchestrates multiple specialized sub-agents to perform comprehensive parallel code review for a pull request (supports both GitHub and Bitbucket).

## Parse PR number and target branch from arguments: $ARGUMENTS

## Instructions:

1. **Parse arguments**:
   - Extract PR number from $ARGUMENTS (required, e.g., "12" or "PR_12" or "PR-12")
   - Extract target branch if provided (optional, defaults to main/master/trunk/develop)
   - Clean PR number to numeric value only (remove "PR_", "PR-", etc.)

2. **Detect repository platform and fetch PR branch**:
   ```bash
   # First, detect if this is GitHub or Bitbucket by checking remote URL
   git remote get-url origin
   # If URL contains github.com: use GitHub format
   #   git fetch origin pull/{PR_NUMBER}/head:pr-{PR_NUMBER}
   # If URL contains bitbucket: use Bitbucket format  
   #   git fetch origin refs/pull-requests/{PR_NUMBER}/from:pr-{PR_NUMBER}
   ```
   Note: Use the cleaned numeric PR number from step 1

3. **Gather git context for all sub-agents**:
   - PR branch: `pr-{PR_NUMBER}` (using numeric value from step 1)
   - Determine target branch: Use provided branch or detect from: main, master, trunk, develop
   - Get merge base: Execute `git merge-base pr-{PR_NUMBER} {target_branch}` and store result in {merge_base}
   - List commits in PR: Execute `git log --oneline {merge_base}..pr-{PR_NUMBER}` after getting merge base
   - Get full diff: Execute `git diff {merge_base}...pr-{PR_NUMBER}` after getting merge base
   - File statistics: Execute `git diff --stat {merge_base}...pr-{PR_NUMBER}` after getting merge base
   - Changed files list: Execute `git diff --name-only {merge_base}...pr-{PR_NUMBER}` after getting merge base

4. **Launch all sub-agents in parallel**:

Task(
  description="Code quality review for PR",
  subagent_type="code-quality-reviewer",
  prompt=f"""Review code quality for Pull Request #{pr_id}
  
  PR Branch: pr-{pr_id}
  Target Branch: {target_branch}
  Merge Base: {merge_base}
  
  COMMITS IN PR:
  {commits_list}
  
  FULL DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  IMPORTANT: For EVERY issue found, you MUST provide:
  1. The problematic code snippet with exact location
  2. The complete fixed/improved code snippet
  3. Clear explanation of why this change improves quality
  4. Reference to best practices or documentation when applicable
  
  Use this format for each issue:
  **Issue**: [description]
  **Location**: [file:line]
  **Category**: [Naming/Structure/Complexity/Duplication/etc]
  
  **Current code**:
  ```language
  [exact problematic code from the diff]
  ```
  
  **Improved code**:
  ```language
  [complete working solution]
  ```
  
  **Explanation**: [why this improves code quality]
  **Best Practice Reference**: [link or standard name if applicable]
  """
)

Task(
  description="Security analysis for PR",
  subagent_type="security-reviewer", 
  prompt=f"""Analyze security vulnerabilities in Pull Request #{pr_id}
  
  PR Branch: pr-{pr_id}
  Target Branch: {target_branch}
  Merge Base: {merge_base}
  
  COMMITS IN PR:
  {commits_list}
  
  FULL DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  Focus on:
  - Input validation and sanitization
  - Authentication and authorization
  - Injection vulnerabilities (SQL, XSS, command, etc.)
  - Sensitive data exposure
  - Cryptographic issues
  - Dependency vulnerabilities
  - CORS and security headers
  - Rate limiting and DoS protection
  
  IMPORTANT: For EVERY vulnerability found, you MUST provide:
  1. The vulnerable code snippet
  2. The complete secure implementation
  3. Potential attack scenario
  4. OWASP or CWE reference when applicable
  
  Use this format for each vulnerability:
  **Vulnerability**: [type and description]
  **Location**: [file:line]
  **Severity**: [Critical/High/Medium/Low]
  **OWASP/CWE**: [reference if applicable]
  
  **Vulnerable code**:
  ```language
  [exact vulnerable code from the diff]
  ```
  
  **Secure implementation**:
  ```language
  [complete secure solution]
  ```
  
  **Attack Scenario**: [how an attacker could exploit this]
  **Mitigation**: [how the fix prevents the vulnerability]
  """
)

Task(
  description="Performance review for PR",
  subagent_type="performance-reviewer",
  prompt=f"""Review performance implications of Pull Request #{pr_id}
  
  PR Branch: pr-{pr_id}
  Target Branch: {target_branch}
  Merge Base: {merge_base}
  
  COMMITS IN PR:
  {commits_list}
  
  FULL DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  Focus on:
  - Algorithm complexity (time and space)
  - Database query optimization
  - N+1 query problems
  - Memory leaks and excessive allocations
  - Caching opportunities
  - Async/parallel processing opportunities
  - Resource cleanup
  - Network request optimization
  
  IMPORTANT: For EVERY performance issue found, you MUST provide:
  1. The slow/inefficient code snippet
  2. The complete optimized implementation
  3. Complexity analysis (before/after)
  4. Estimated performance impact
  
  Use this format for each issue:
  **Issue**: [performance problem]
  **Location**: [file:line]
  **Impact**: [Critical/High/Medium/Low]
  **Complexity**: [O(n²) → O(n) for example]
  
  **Current implementation**:
  ```language
  [exact slow code from the diff]
  ```
  
  **Optimized implementation**:
  ```language
  [complete optimized solution]
  ```
  
  **Performance gain**: [estimated improvement, e.g., "50% faster", "reduces memory by 30%"]
  **Explanation**: [why this optimization works]
  **Trade-offs**: [any downsides to consider]
  """
)

Task(
  description="Testing assessment for PR",
  subagent_type="testing-reviewer",
  prompt=f"""Assess testing coverage and quality for Pull Request #{pr_id}
  
  PR Branch: pr-{pr_id}
  Target Branch: {target_branch}
  Merge Base: {merge_base}
  
  COMMITS IN PR:
  {commits_list}
  
  FULL DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  Analyze:
  - New code without tests
  - Modified code with broken tests
  - Edge cases not covered
  - Error scenarios not tested
  - Integration test needs
  - Test quality and maintainability
  - Mock/stub appropriateness
  - Test naming and documentation
  
  IMPORTANT: For EVERY testing gap found, you MUST provide:
  1. The untested code
  2. Complete test implementation
  3. Test scenarios that should be covered
  4. Test framework/patterns to use
  
  Use this format for each gap:
  **Missing Test**: [what needs testing]
  **Location**: [file/function needing tests]
  **Test Type**: [unit/integration/e2e]
  **Priority**: [Critical/High/Medium/Low]
  
  **Code to test**:
  ```language
  [exact code needing tests from the diff]
  ```
  
  **Test implementation**:
  ```language
  [complete test code using appropriate framework]
  ```
  
  **Test scenarios covered**:
  - ✓ [happy path scenario]
  - ✓ [edge case 1]
  - ✓ [edge case 2]
  - ✓ [error scenario]
  
  **Coverage improvement**: [e.g., "increases coverage from 60% to 85%"]
  """
)

Task(
  description="Bug detection for PR",
  subagent_type="bug-detector",
  prompt=f"""Detect potential bugs and edge cases in Pull Request #{pr_id}
  
  PR Branch: pr-{pr_id}
  Target Branch: {target_branch}
  Merge Base: {merge_base}
  
  COMMITS IN PR:
  {commits_list}
  
  FULL DIFF:
  {git_diff}
  
  FILES CHANGED:
  {file_stats}
  
  Look for:
  - Null/undefined reference errors
  - Off-by-one errors
  - Race conditions
  - Resource leaks
  - Incorrect error handling
  - Type mismatches
  - Logic errors
  - Edge case handling
  - Boundary conditions
  - Concurrency issues
  
  IMPORTANT: For EVERY bug found, you MUST provide:
  1. The buggy code snippet
  2. The complete fixed implementation
  3. Scenario that triggers the bug
  4. Impact assessment
  
  Use this format for each bug:
  **Bug Type**: [category and description]
  **Location**: [file:line]
  **Severity**: [Critical/High/Medium/Low]
  **Likelihood**: [High/Medium/Low]
  
  **Problematic code**:
  ```language
  [exact buggy code from the diff]
  ```
  
  **Fixed implementation**:
  ```language
  [complete corrected code]
  ```
  
  **Trigger scenario**: [step-by-step how to reproduce]
  **Impact**: [what happens when the bug occurs]
  **Root cause**: [why the bug exists]
  **Prevention**: [how the fix prevents the bug]
  """
)

5. **Additional context checks** (run in parallel with agents):
   - Check for uncommitted changes: Execute `git status --porcelain`
   - Check for merge conflicts: Execute `git diff --check pr-{PR_NUMBER}...{target_branch}`
   - Check PR size: Count total lines changed and flag if > 500 lines

6. **Aggregate results from all sub-agents**:

## Final Report Format:

### 📊 Pull Request Summary
- **PR ID**: #{pr_id}
- **PR Branch**: pr-{pr_id} → **Target Branch**: {target_branch}
- **Total commits**: {commit_count}
- **Files changed**: {file_count}
- **Lines changed**: +{lines_added} / -{lines_removed}
- **PR Size**: {small/medium/large/extra-large based on lines}
- **Overall Risk Level**: {calculated from all agent findings}

### 🎯 Review Results by Category

#### 🎨 Code Quality
*From code-quality-reviewer agent*
{quality_findings}

#### 🔒 Security Analysis
*From security-reviewer agent*
{security_findings}

#### ⚡ Performance Review
*From performance-reviewer agent*
{performance_findings}

#### 🧪 Testing Coverage
*From testing-reviewer agent*
{testing_findings}

#### 🐛 Bug Detection
*From bug-detector agent*
{bug_findings}

### 📋 Consolidated Action Items

#### 🚨 Critical (Must fix before merge)
{For each critical issue from any agent, include:
- Issue description
- Location
- The complete code fix provided by the agent
- Which agent found it}

#### ⚠️ Important (Should fix)
{For each important issue from any agent, include:
- Issue description  
- Location
- The complete code fix provided by the agent
- Which agent found it}

#### 💡 Suggestions (Nice to have)
{For each suggestion from any agent, include:
- Issue description
- Location
- The improvement provided by the agent
- Which agent found it}

### 🔧 Quick Fix Examples

{Top 5 most critical fixes with before/after code snippets for easy copy-paste}

**1. {Most critical issue title}**
```language
// ❌ Before (Line {line} in {file}):
{problematic_code}

// ✅ After:
{fixed_code}
```

**2. {Second most critical issue}**
...

### ✅ Positive Highlights
{Aggregate positive findings from all agents}
- Well-implemented features
- Good practices observed
- Performance optimizations noted
- Good test coverage areas
- Security best practices followed

### 📈 Metrics Summary
- **Code Quality Score**: {X}/10
- **Security Score**: {X}/10  
- **Performance Impact**: {positive/neutral/negative}
- **Test Coverage Delta**: {+X%/-X%}
- **Bug Risk Level**: {low/medium/high}

### 🔄 PR Readiness
**Ready to merge**: {Yes/No}

{If No, list blocking issues}

**Recommended actions**:
1. {First priority action}
2. {Second priority action}
3. {Third priority action}

---
*Review completed for PR #{pr_id} using 5 specialized analysis agents*