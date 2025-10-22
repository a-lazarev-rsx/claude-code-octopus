---
description: Performs comprehensive code review directly without using sub-agents
---

# Direct Code Review

Performs comprehensive code review analyzing code quality, security, performance, testing, and bugs directly.

## Instructions:

1. First, gather git context:
   - Current branch: !`git branch --show-current`
   - Parent branch detection: !`git merge-base --fork-point main HEAD 2>/dev/null || git merge-base --fork-point master HEAD 2>/dev/null || git merge-base --fork-point develop HEAD 2>/dev/null || echo ""`
   - List commits: !`git log --oneline HEAD --not $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))`
   - Get full diff: !`git diff $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`
   - File statistics: !`git diff --stat $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`
   - Changed files list: !`git diff --name-only $(git merge-base HEAD $(git branch -r | grep -E 'origin/(main|master|develop)' | head -1 | sed 's/origin\///'))...HEAD`

2. Analyze the changes systematically:

### Code Quality Analysis
Review the diff for:
- Code style and naming conventions
- Function/method complexity and readability
- Proper error handling
- Code duplication (DRY principle violations)
- SOLID principles adherence
- Proper use of design patterns
- Clear and maintainable code structure

For each issue found, provide:
- **Issue**: [description]
- **Location**: [file:line]
- **Current code**: [problematic snippet]
- **Fixed code**: [improved version]
- **Explanation**: [why this fix improves quality]

### Security Analysis
Check for:
- SQL injection vulnerabilities
- XSS (Cross-Site Scripting) risks
- CSRF vulnerabilities
- Insecure data storage
- Hardcoded credentials or secrets
- Improper input validation
- Authentication/authorization issues
- Sensitive data exposure
- Insecure dependencies
- Path traversal vulnerabilities

For each vulnerability, provide:
- **Vulnerability**: [type and description]
- **Location**: [file:line]
- **Severity**: [Critical/High/Medium/Low]
- **Vulnerable code**: [exact code]
- **Secure code**: [fixed version]
- **Explanation**: [how this prevents the vulnerability]

### Performance Analysis
Identify:
- N+1 query problems
- Unnecessary loops or iterations
- Inefficient algorithms (O(n²) when O(n) is possible)
- Memory leaks or excessive memory usage
- Synchronous operations that should be async
- Missing caching opportunities
- Redundant calculations
- Inefficient database queries
- Large unbounded data operations

For each issue, provide:
- **Issue**: [performance problem]
- **Location**: [file:line]
- **Impact**: [High/Medium/Low]
- **Current implementation**: [slow code]
- **Optimized code**: [improved version]
- **Performance gain**: [estimated improvement]
- **Explanation**: [why this is faster]

### Testing Assessment
Evaluate:
- Test coverage for new code
- Edge cases coverage
- Error condition testing
- Integration test needs
- Unit test quality
- Mock/stub usage appropriateness
- Test maintainability
- Test performance

For each gap, provide:
- **Missing Test**: [what needs testing]
- **Location**: [file/function needing tests]
- **Priority**: [Critical/High/Medium/Low]
- **Code to test**: [exact code]
- **Test implementation**: [complete test code]
- **Test scenarios covered**: [list of scenarios]

### Bug Detection
Look for:
- Null/undefined reference errors
- Off-by-one errors
- Race conditions
- Deadlocks
- Type mismatches
- Logic errors
- Edge case handling issues
- Resource leaks
- Incorrect error handling
- State management issues

For each bug, provide:
- **Bug Type**: [category and description]
- **Location**: [file:line]
- **Severity**: [Critical/High/Medium/Low]
- **Problematic code**: [buggy code]
- **Fixed code**: [corrected version]
- **Scenario to trigger**: [how to reproduce]
- **Explanation**: [why the fix prevents the bug]

3. Generate the final report:

## 📊 Code Review Report

### Executive Summary
- **Branch**: {current_branch} → {parent_branch}
- **Commits**: {total_commits}
- **Files changed**: {files_changed}
- **Lines**: +{lines_added} / -{lines_removed}
- **Overall Risk Level**: [Critical/High/Medium/Low]

### 🎯 Critical Issues (Must Fix Before Merge)
[List all critical findings with code examples]

### ⚠️ Important Issues (Should Fix)
[List all important findings with code examples]

### 💡 Suggestions (Nice to Have)
[List minor improvements and suggestions]

### ✅ Positive Highlights
- Well-structured code in: [files]
- Good practices observed: [list]
- Proper error handling in: [locations]
- Efficient implementations: [list]

### 📈 Metrics Summary
- **Security Issues**: {count} (Critical: {x}, High: {y}, Medium: {z})
- **Performance Issues**: {count}
- **Code Quality Issues**: {count}
- **Bug Risks**: {count}
- **Test Coverage Gaps**: {count}

### 🔧 Quick Fixes Reference
[Provide top 5-10 most important fixes with before/after code snippets]

Example:
**1. Fix SQL Injection Vulnerability**
```csharp
// Before:
string query = $"SELECT * FROM Users WHERE Id = {userId}";
var result = context.Database.SqlQuery<User>(query);

// After:
var result = context.Users
    .FromSqlRaw("SELECT * FROM Users WHERE Id = @userId", new SqlParameter("@userId", userId))
    .ToList();
```

**2. Fix N+1 Query Problem**
```csharp
// Before:
foreach (var order in orders)
{
    var customer = context.Customers.Find(order.CustomerId);
    // Process customer
}

// After:
var ordersWithCustomers = context.Orders
    .Include(o => o.Customer)
    .ToList();
```

**3. Fix Null Reference Exception**
```csharp
// Before:
var length = user.Address.Street.Length;

// After:
var length = user?.Address?.Street?.Length ?? 0;
```

**4. Fix Resource Leak**
```csharp
// Before:
var connection = new SqlConnection(connectionString);
connection.Open();
// Use connection

// After:
using (var connection = new SqlConnection(connectionString))
{
    connection.Open();
    // Use connection
} // Automatically disposed
```

**5. Fix Async Deadlock**
```csharp
// Before:
var result = GetDataAsync().Result;

// After:
var result = await GetDataAsync();
// Or if synchronous context required:
// Preferred: make the calling method async and use await
var result = await GetDataAsync().ConfigureAwait(false);
```

### 📝 Action Items Checklist
- [ ] Fix all critical security vulnerabilities
- [ ] Address performance bottlenecks
- [ ] Add missing test coverage
- [ ] Fix identified bugs
- [ ] Improve code quality issues
- [ ] Update documentation if needed

### 📚 Additional Notes
[Any context-specific observations or recommendations]

## Additional context: $ARGUMENTS