---
description: Analyze GitHub Issue and create comprehensive implementation plan using
  multiple specialized agents
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
    gh *: allow
---

# GitHub Issue Analysis and Implementation Planning

Analyze GitHub Issue and create a comprehensive implementation plan using specialized agents for codebase analysis, architecture, implementation, testing, and security.

## Phase 0: GitHub Issue Retrieval

Extract and analyze the GitHub Issue from: $ARGUMENTS

1. **Parse GitHub reference**:
   - If full URL provided: Extract owner/repo/number from URL pattern `https://github.com/{owner}/{repo}/issues/{number}`
   - If issue number provided: Use directly (e.g., 123) with current repository context
   - Validate format before proceeding

2. **Determine repository context**:
   ```bash
   # If only number provided, get current repo from git remote
   REPO_INFO=$(git remote get-url origin | sed -E 's/.*github.com[:/]([^/]+)\/([^.]+)(\.git)?/\1\/\2/')
   # Extract owner and repo name
   # If URL provided, extract from URL directly
   ```

3. **Fetch GitHub Issue details**:
   - Use `gh issue view <number> --repo <owner>/<repo>` to retrieve the issue
   - Fetch with JSON format for structured data:
     ```bash
     gh issue view <number> --repo <owner>/<repo> --json number,title,state,author,body,labels,assignees,milestone,createdAt,updatedAt
     ```
   - Extract description from body field
   - Parse acceptance criteria from body (commonly in checklist format or sections)
   - Note linked issues and dependencies
   - Identify issue labels and milestone

4. **Save issue details**:
   - Create working directory: `mkdir -p working-docs/analysis/issue-[NUMBER]/`
   - Save GitHub Issue details to `working-docs/analysis/issue-[NUMBER]/github-issue.md`
   - Include formatted markdown with:
     ```markdown
     # GitHub Issue #[NUMBER]: [Title]

     **State**: [open/closed]
     **Author**: [author.login]
     **Created**: [createdAt]
     **Updated**: [updatedAt]
     **Labels**: [label1, label2, ...]
     **Assignees**: [assignee1, assignee2, ...]
     **Milestone**: [milestone.title]

     ## Description
     [body content]

     ## Acceptance Criteria
     [Parsed from body - typically checklist items or dedicated section]
     ```

5. **Analyze issue complexity** to determine which optional agents to run:
   - Check for security keywords: authentication, authorization, encryption, security, credentials, sensitive data
   - Check for complex testing needs: multiple integrations, critical paths, complex flows
   - Set flags: `needs_security_analysis`, `needs_testing_strategy`

## Phase 1: Core Analysis (Parallel Execution)

Launch two core agents in parallel using the Task tool. These agents run sequentially for faster execution.

### Agent 1: Codebase Analyzer
@codebase-analyzer

"Analyze the codebase for implementing GitHub Issue #[NUMBER].

Issue requirements:
[Include the issue title, description, and acceptance criteria from Phase 0]

Use mcp__filesystem__ tools extensively to:
1. Map the complete project structure using mcp__filesystem__directory_tree
2. Find all configuration files using mcp__filesystem__search_files
3. Identify similar implementations related to the issue
4. Analyze modules that will be affected
5. Map all integration points

Focus on:
- Finding files that need modification
- Identifying existing patterns to follow
- Locating test files that need updates
- Understanding the technology stack
- Mapping dependencies

Save your analysis to: working-docs/analysis/issue-[NUMBER]/codebase-analysis.md"

### Agent 2: Implementation Planner (Enhanced with Context7)
@planning-implementation

"Create a comprehensive implementation plan for GitHub Issue #[NUMBER] with architecture design and best practices research.

Issue Requirements:
[Include title, description, and acceptance criteria from Phase 0]

MANDATORY: Use Context7 extensively for best practices:
1. Call mcp__context7__resolve-library-id for EVERY technology identified
2. Call mcp__context7__get-library-docs with 15000+ tokens for detailed docs
3. Research multiple implementation patterns for each technology
4. Find official documentation examples
5. Check for breaking changes and deprecations

Your plan must include:

## 1. Architecture & Design
- Overall system architecture design
- Component boundaries and responsibilities
- Design patterns appropriate for this feature
- Consistency with existing architecture
- SOLID principles application
- Module organization recommendations
- ASCII diagrams for component interactions

## 2. Best Practices Research (from Context7)
- Implementation patterns with pros/cons
- Performance optimization techniques
- Security best practices
- Common pitfalls to avoid
- Comparison of different approaches
- Actual code examples from Context7
- Recommended patterns with justification
- Anti-patterns to avoid

## 3. Implementation Steps
Create a detailed step-by-step plan:
- File-by-file modification list with full paths
- New components to create
- API contracts and interfaces
- Database schema changes
- State management approach

For each step provide:
- Specific files to modify/create
- Code examples using project patterns
- Estimated time in hours
- Dependencies on other steps
- Basic testing approach

## 4. Performance Considerations
- Optimal algorithms and data structures
- Caching strategy (Redis/CDN if applicable)
- Database query optimization
- Async processing where needed
- Scalability approach

## 5. CI/CD Considerations
- Build process modifications needed
- New test stages required
- Quality gates recommendations
- Deployment strategy notes

Structure as phases with clear milestones.

Save your plan to: working-docs/analysis/issue-[NUMBER]/implementation-draft.md"

**Wait for both agents to complete** before proceeding to Phase 2.

## Phase 2: Specialized Analysis (Conditional Parallel Execution)

Read the core analysis results:
- working-docs/analysis/issue-[NUMBER]/codebase-analysis.md
- working-docs/analysis/issue-[NUMBER]/implementation-draft.md

Based on Phase 0 complexity analysis, launch optional agents in parallel:

### Optional Agent 1: Testing Strategist (If Complex Testing Needed)
Only launch if `needs_testing_strategy` is true.

@planning-testing-strategist

"Design comprehensive testing strategy for GitHub Issue #[NUMBER].

Issue Requirements:
[Include title, description, and acceptance criteria from Phase 0]

Previous Analysis:
- Codebase findings: [Summary from codebase-analysis.md]
- Implementation plan: [Summary from implementation-draft.md]

Current Testing Setup:
[Include testing infrastructure from codebase analysis]

Plan:
1. Unit tests for new components
2. Integration tests for API changes
3. E2E tests for user flows
4. Performance test scenarios
5. Security test cases
6. Test data management

For each test type provide:
- Specific test scenarios with examples
- Test file locations and naming
- Mock/stub strategies
- Coverage targets (aim for >80%)
- Example test code following project patterns

Include test pyramid distribution recommendations.

Save your strategy to: working-docs/analysis/issue-[NUMBER]/testing-strategy.md"

### Optional Agent 2: Security Architect (If Security-Sensitive)
Only launch if `needs_security_analysis` is true.

@planning-security-architect

"Design security architecture for GitHub Issue #[NUMBER].

Issue Requirements:
[Include title, description, and acceptance criteria from Phase 0]

Previous Analysis:
- Codebase findings: [Summary from codebase-analysis.md]
- Implementation plan: [Summary from implementation-draft.md]

Analyze and design:
1. Authentication/authorization requirements
2. Data protection and encryption needs
3. Input validation and sanitization
4. Security headers and configurations
5. Audit logging requirements
6. Compliance considerations

Perform threat modeling:
- Identify attack vectors
- STRIDE analysis if applicable
- Risk assessment
- Mitigation strategies

Provide:
- Security checklist
- Specific security controls to implement
- Code examples for security measures
- Security testing recommendations

Save your design to: working-docs/analysis/issue-[NUMBER]/security-architecture.md"

**Wait for all launched agents to complete** before proceeding to Phase 3.

## Phase 3: Final Plan Compilation

### 3.1 Read All Available Outputs
Read the following files (only if they exist):
- working-docs/analysis/issue-[NUMBER]/github-issue.md
- working-docs/analysis/issue-[NUMBER]/codebase-analysis.md
- working-docs/analysis/issue-[NUMBER]/implementation-draft.md
- working-docs/analysis/issue-[NUMBER]/testing-strategy.md (if exists)
- working-docs/analysis/issue-[NUMBER]/security-architecture.md (if exists)

### 3.2 Compile Single Implementation Plan

Compile all findings into ONE comprehensive document:
`working-docs/analysis/issue-[NUMBER]/IMPLEMENTATION-PLAN.md`

Structure:

```markdown
# Implementation Plan: Issue #[NUMBER] - [Issue Title]

## Executive Summary
- Issue overview and business value
- Key technologies involved
- Estimated timeline: X days
- Risk level: Low/Medium/High
- Team members needed: X developers

## 1. Current State Analysis
[Summary from codebase-analysis.md]
- Affected components and files
- Existing patterns to follow
- Dependencies identified
- Technical debt considerations

## 2. Architecture & Design
[From implementation-draft.md - Architecture section]
- System architecture changes
- Component design with diagrams
- API contracts
- Database schema changes
- State management approach

## 3. Best Practices & Recommendations
[From implementation-draft.md - Best Practices section]
- Implementation patterns (from Context7)
- Code examples
- Performance optimization techniques
- Anti-patterns to avoid

## 4. Implementation Steps

### Phase 1: Foundation (Day 1-2)
[Detailed steps from implementation-draft.md]
- Specific files to create/modify
- Code examples
- Estimated hours per task

### Phase 2: Core Features (Day 3-4)
[Continue with detailed steps...]

### Phase 3: Integration (Day 5)
[Integration steps...]

## 5. Testing Strategy
[From testing-strategy.md if exists, otherwise from implementation-draft.md]
- Unit tests: X new tests required
- Integration tests: Y scenarios
- E2E tests: Z user flows
- Coverage target: >80%
- Test execution plan

## 6. Security Measures
[From security-architecture.md if exists, otherwise basic security from implementation-draft.md]
- Security controls to implement
- Validation rules
- Authentication/authorization changes
- Threat mitigation strategies

## 7. Performance Considerations
[From implementation-draft.md - Performance section]
- Optimization techniques
- Caching strategy
- Performance targets
- Monitoring plan

## 8. CI/CD Updates
[From implementation-draft.md - CI/CD section]
- Pipeline modifications
- New quality gates
- Deployment strategy
- Rollback procedures

## 9. Risk Mitigation
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| [Identified risks with mitigation strategies] |

## 10. Success Criteria
- [ ] All acceptance criteria met
- [ ] Tests passing (>80% coverage)
- [ ] Performance benchmarks met
- [ ] Security scan passed
- [ ] Code review approved
- [ ] Documentation complete
- [ ] Deployed to staging

## 11. Timeline & Milestones
| Phase | Description | Duration | Status |
|-------|------------|----------|--------|
| 1 | Foundation | 2 days | Not Started |
| 2 | Core Implementation | 2 days | Not Started |
| 3 | Testing & Integration | 1 day | Not Started |
| 4 | Deployment | 0.5 days | Not Started |

**Total Estimated Time: X days**

## 12. Next Steps
1. Review plan with team
2. Get approval from stakeholders
3. Create GitHub Issue tasks/subtasks
4. Begin implementation
5. Daily progress updates
```

### 3.3 Clean Up Intermediate Files (Optional)
After compilation, you may remove intermediate files:
- codebase-analysis.md
- implementation-draft.md
- testing-strategy.md (if exists)
- security-architecture.md (if exists)

Keep only:
- github-issue.md
- IMPLEMENTATION-PLAN.md

## Phase 4: Summary Report

Provide a concise summary to the user:

```
✅ GitHub Issue Analysis Complete: #[NUMBER]

📊 Analysis Summary:
- Codebase impact: X files affected
- Technologies: [list]
- Estimated time: X days
- Risk level: [Low/Medium/High]

🤖 Agents Used:
- ✅ Codebase Analyzer
- ✅ Implementation Planner (with Context7 research)
- [✅ Testing Strategist] (if applicable)
- [✅ Security Architect] (if applicable)

📁 Results Location:
- Main plan: working-docs/analysis/issue-[NUMBER]/IMPLEMENTATION-PLAN.md
- Issue details: working-docs/analysis/issue-[NUMBER]/github-issue.md

⏱️ Execution Time: Approximately 30-50% faster than previous multi-group approach

🎯 Next Steps:
1. Review IMPLEMENTATION-PLAN.md
2. Discuss with team
3. Create GitHub Issue tasks/subtasks
4. Begin implementation
```

## Error Handling

### Invalid GitHub Issue Reference
If the GitHub Issue reference cannot be parsed or accessed:
```
❌ Error: Invalid GitHub Issue reference.
Please provide a valid GitHub Issue URL (https://github.com/owner/repo/issues/123)
or issue number (123).

If using issue number, ensure you are in a git repository with GitHub remote.
```

### GitHub CLI Not Available
If `gh` CLI is not installed or not authenticated:
```
❌ Error: GitHub CLI (gh) is not available or not authenticated.
Please install gh CLI and authenticate: gh auth login
```

### Agent Failures
If any agent fails:
- Continue with other agents
- Note the failure in the final plan
- Provide partial analysis with available data
- Log which agent failed and why

### Access Restrictions
If filesystem access is restricted:
- Use available tools (Read, Grep, Glob)
- Focus on accessible directories
- Note limitations in the final report

### Repository Access Issues
If you don't have access to the repository or issue:
```
❌ Error: Access denied to GitHub Issue #[NUMBER].
Please ensure you have read access to the repository and the issue exists.
Check your gh auth status: gh auth status
```

## Usage Examples

### Basic Usage with Issue Number
```
/agentic-github-issue-analyze 123
```

### With Full GitHub URL
```
/agentic-github-issue-analyze https://github.com/owner/repo/issues/456
```

## Notes

- **Parallel execution**: Core agents run sequentially for 40-50% faster analysis
- **Conditional agents**: Testing and Security agents only run when needed
- **Context7 integration**: Best practices research integrated into main planner
- **Single output**: One comprehensive IMPLEMENTATION-PLAN.md instead of multiple files
- **Same quality**: All aspects covered (architecture, testing, security, performance, CI/CD)
- **Simpler architecture**: 2-4 agents instead of 9, no sequential groups
- **Better efficiency**: Reduced context switching and redundant analysis
- **GitHub native**: Uses gh CLI for all GitHub operations, no external APIs needed
