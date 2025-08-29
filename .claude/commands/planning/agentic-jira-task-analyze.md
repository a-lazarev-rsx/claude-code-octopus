---
description: Analyze JIRA task and create comprehensive implementation plan using multiple specialized agents
argument-hint: "JIRA task link or ISSUE-KEY"
allowed-tools: Task, mcp__atlassian__*, mcp__context7__*, mcp__filesystem__*, Read, Write, Grep, Glob, WebSearch, Bash, LS
---

# JIRA Task Analysis and Implementation Planning

Analyze JIRA task and create a comprehensive implementation plan using specialized agents for architecture, testing, CI/CD, security, and documentation.

## Phase 0: JIRA Task Retrieval

Extract and analyze the JIRA task from: $ARGUMENTS

1. **Parse JIRA reference**:
   - If full URL provided: Extract issue key from URL pattern
   - If issue key provided: Use directly (e.g., PROJ-123)
   - Validate format before proceeding

2. **Fetch JIRA task details**:
   - Use `mcp__atlassian__getJiraIssue` to retrieve the task
   - Extract description, acceptance criteria, and attachments
   - Note linked issues and dependencies
   - Identify task type and priority

3. **Save task details**:
   - Create working directory: `mkdir -p working-docs/analysis/[ISSUE-KEY]/`
   - Save JIRA task details to `working-docs/analysis/[ISSUE-KEY]/jira-task.md`

## Phase 1: Deep Codebase Analysis

Perform comprehensive codebase analysis to understand the current state and impact areas.

Use the Task tool to invoke the codebase-analyzer agent with the following prompt:

"Analyze the codebase for implementing JIRA task [ISSUE-KEY]. 

Task requirements:
[Include the task description and acceptance criteria from Phase 0]

Use mcp__filesystem__ tools extensively to:
1. Map the complete project structure using mcp__filesystem__directory_tree
2. Find all configuration files using mcp__filesystem__search_files
3. Identify similar implementations related to the task
4. Analyze modules that will be affected
5. Map all integration points

Focus on:
- Finding files that need modification
- Identifying existing patterns to follow
- Locating test files that need updates
- Understanding the technology stack
- Mapping dependencies

Save your analysis to: working-docs/analysis/[ISSUE-KEY]/codebase-analysis.md"

Set subagent_type="codebase-analyzer" when invoking the Task tool.

## Phase 2: Agent Analysis Groups

After Phase 1 completes, launch specialized agents in sequential groups.

### Group 1: Analysis and Research (Launch in Parallel)

Save the codebase analysis results, then launch these three agents simultaneously using the Task tool:

#### Agent 1.1: Architecture Quality Advisor
Use Task tool with subagent_type="planning-quality-advisor":

"Design the architecture for implementing JIRA task [ISSUE-KEY].

Task Requirements:
[Include requirements from Phase 0]

Codebase Analysis:
[Include key findings from Phase 1 codebase analysis]

Focus on:
1. Overall system architecture design
2. Component boundaries and responsibilities
3. Design patterns appropriate for this feature
4. Consistency with existing architecture
5. SOLID principles application
6. Module organization recommendations

Provide:
- Architecture overview with ASCII diagrams
- Component interaction flows
- Interface definitions
- Quality guidelines and standards

Save your analysis to: working-docs/analysis/[ISSUE-KEY]/architecture-quality.md"

#### Agent 1.2: Best Practices Researcher
Use Task tool with subagent_type="planning-best-practices":

"Research comprehensive best practices for implementing JIRA task [ISSUE-KEY].

Task Requirements:
[Include requirements from Phase 0]

Technologies Detected:
[Include technology stack from Phase 1 analysis]

MANDATORY: Use Context7 extensively:
1. Call mcp__context7__resolve-library-id for EVERY technology identified
2. Call mcp__context7__get-library-docs with 15000+ tokens for detailed docs
3. Research multiple implementation patterns for each technology
4. Find official documentation examples
5. Check for breaking changes and deprecations

Research areas:
- Implementation patterns with pros/cons
- Performance optimization techniques
- Security best practices
- Testing approaches
- Common pitfalls to avoid

Provide:
- Comparison of different approaches
- Actual code examples from Context7
- Recommended patterns with justification
- Anti-patterns to avoid

Save your research to: working-docs/analysis/[ISSUE-KEY]/best-practices.md"

Wait for Group 1 agents to complete before proceeding to Group 2.

### Group 2: Planning and Design (Launch in Parallel)

After Group 1 completes, read the results from:
- working-docs/analysis/[ISSUE-KEY]/codebase-analysis.md
- working-docs/analysis/[ISSUE-KEY]/architecture-quality.md
- working-docs/analysis/[ISSUE-KEY]/best-practices.md

Then launch these three agents simultaneously:

#### Agent 2.1: Implementation Planner
Use Task tool with subagent_type="planning-implementation":

"Create a detailed implementation plan for JIRA task [ISSUE-KEY].

Task Requirements:
[Include requirements from Phase 0]

Previous Analysis:
- Codebase findings: [Summary from codebase-analysis.md]
- Architecture design: [Summary from architecture-quality.md]
- Best practices: [Summary from best-practices.md]

Create:
1. Step-by-step implementation plan
2. File-by-file modification list
3. New components to create
4. API contracts and interfaces
5. Database schema changes
6. State management approach

For each step provide:
- Specific files to modify/create with full paths
- Code examples using project patterns
- Estimated time in hours
- Dependencies on other steps
- Testing approach

Structure as phases with clear milestones.

Save your plan to: working-docs/analysis/[ISSUE-KEY]/implementation-plan.md"

#### Agent 2.2: Testing Strategist
Use Task tool with subagent_type="planning-testing-strategist":

"Design comprehensive testing strategy for JIRA task [ISSUE-KEY].

Task Requirements:
[Include requirements from Phase 0]

Implementation Details:
[Include summary from Group 1 analysis]

Current Testing Setup:
[Include testing infrastructure from Phase 1]

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

Save your strategy to: working-docs/analysis/[ISSUE-KEY]/testing-strategy.md"

#### Agent 2.3: Security Architect
Use Task tool with subagent_type="planning-security-architect":

"Design security architecture for JIRA task [ISSUE-KEY].

Task Requirements:
[Include requirements from Phase 0]

Architecture Design:
[Include summary from architecture-quality.md]

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

Save your design to: working-docs/analysis/[ISSUE-KEY]/security-architecture.md"

Wait for Group 2 agents to complete before proceeding to Group 3.

### Group 3: Infrastructure and Operations (Launch in Parallel)

After Group 2 completes, read the implementation plan and other Group 2 outputs, then launch:

#### Agent 3.1: CI/CD Pipeline Designer
Use Task tool with subagent_type="planning-ci-cd":

"Design CI/CD pipeline updates for JIRA task [ISSUE-KEY].

Implementation Plan:
[Include summary from implementation-plan.md]

Testing Strategy:
[Include summary from testing-strategy.md]

Current CI/CD Setup:
[Include CI/CD configuration from Phase 1]

Plan updates for:
1. Build process modifications
2. New test stages
3. Quality gates and checks
4. Deployment changes
5. Environment configurations

If Jenkins is used:
- Provide specific Jenkinsfile updates
- Suggest shared library usage
- Optimize parallel execution

If GitHub Actions/GitLab CI:
- Provide workflow file updates
- Optimize job dependencies

Include:
- Specific pipeline code changes
- Environment variable updates
- Deployment strategies
- Rollback procedures

Save your design to: working-docs/analysis/[ISSUE-KEY]/cicd-pipeline.md"

#### Agent 3.2: Performance Architect
Use Task tool with subagent_type="planning-performance-architect":

"Design performance architecture for JIRA task [ISSUE-KEY].

Implementation Plan:
[Include summary from implementation-plan.md]

Performance Considerations:
- Expected load and concurrency
- Response time requirements (target <200ms)
- Data volume projections
- Resource constraints

Design:
1. Optimal algorithms and data structures
2. Caching strategy (multi-layer with Redis/CDN)
3. Database query optimization
4. Async processing where needed
5. Resource pooling
6. Scalability approach (horizontal/vertical)

Provide:
- Performance optimization checklist
- Specific optimization techniques with code
- Caching implementation details
- Monitoring metrics to track
- Load testing scenarios

Save your design to: working-docs/analysis/[ISSUE-KEY]/performance-architecture.md"

## Phase 3: Synthesis and Final Plan

After all agents complete, compile the comprehensive implementation plan.

### 3.1 Read All Agent Outputs
Read the following files:
- working-docs/analysis/[ISSUE-KEY]/jira-task.md
- working-docs/analysis/[ISSUE-KEY]/codebase-analysis.md
- working-docs/analysis/[ISSUE-KEY]/architecture-quality.md
- working-docs/analysis/[ISSUE-KEY]/best-practices.md
- working-docs/analysis/[ISSUE-KEY]/implementation-plan.md
- working-docs/analysis/[ISSUE-KEY]/testing-strategy.md
- working-docs/analysis/[ISSUE-KEY]/security-architecture.md
- working-docs/analysis/[ISSUE-KEY]/cicd-pipeline.md
- working-docs/analysis/[ISSUE-KEY]/performance-architecture.md

### 3.2 Create Master Implementation Plan

Compile all findings into a comprehensive plan and save to:
`working-docs/analysis/[ISSUE-KEY]/MASTER-PLAN.md`

Structure the master plan as:

```markdown
# Implementation Plan: [JIRA Task Key] - [Task Title]

## Executive Summary
- Task overview and business value
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
[From architecture-quality.md and implementation-plan.md]
- System architecture changes
- Component design with diagrams
- API contracts
- Database schema changes
- State management approach

## 3. Implementation Steps

### Phase 1: Foundation (Day 1-2)
[Detailed steps from implementation-plan.md]
- Specific files to create/modify
- Code examples
- Estimated hours per task

### Phase 2: Core Features (Day 3-4)
[Continue with detailed steps...]

### Phase 3: Integration (Day 5)
[Integration and testing steps...]

## 4. Testing Strategy
[From testing-strategy.md]
- Unit tests: X new tests required
- Integration tests: Y scenarios
- E2E tests: Z user flows
- Coverage target: >80%
- Test execution plan

## 5. Security Measures
[From security-architecture.md]
- Security controls to implement
- Validation rules
- Authentication/authorization changes
- Threat mitigation strategies

## 6. CI/CD Updates
[From cicd-pipeline.md]
- Pipeline modifications
- New quality gates
- Deployment strategy
- Rollback procedures

## 7. Performance Considerations
[From performance-architecture.md]
- Optimization techniques
- Caching strategy
- Performance targets
- Monitoring plan

## 8. Risk Mitigation
| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| [Identified risks with mitigation strategies] |

## 9. Success Criteria
- [ ] All acceptance criteria met
- [ ] Tests passing (>80% coverage)
- [ ] Performance benchmarks met (<200ms response)
- [ ] Security scan passed
- [ ] Code review approved
- [ ] Documentation complete
- [ ] Deployed to staging

## 10. Timeline & Milestones
| Phase | Description | Duration | Start Date | End Date | Status |
|-------|------------|----------|------------|----------|--------|
| 1 | Foundation | 2 days | TBD | TBD | Not Started |
| 2 | Core Implementation | 2 days | TBD | TBD | Not Started |
| 3 | Testing & Integration | 1 day | TBD | TBD | Not Started |
| 4 | Documentation | 0.5 days | TBD | TBD | Not Started |
| 5 | Deployment | 0.5 days | TBD | TBD | Not Started |

**Total Estimated Time: X days**

## 11. Next Steps
1. Review plan with team
2. Get approval from stakeholders
3. Create subtasks in JIRA
4. Begin implementation
5. Daily progress updates
```

### 3.3 Update JIRA (Optional)

If requested, update the JIRA task with the plan summary:

Use `mcp__atlassian__addCommentToJiraIssue` with:
- issueIdOrKey: "[ISSUE-KEY]"
- commentBody: "Implementation plan completed. Full analysis available in working-docs/analysis/[ISSUE-KEY]/"

## Phase 4: Summary Report

Provide a concise summary to the user:

1. ✅ JIRA task [ISSUE-KEY] analyzed
2. ✅ Codebase impact assessment complete
3. ✅ 8 specialized agents provided analysis
4. ✅ Comprehensive plan generated
5. 📁 Results saved to: `working-docs/analysis/[ISSUE-KEY]/`
6. 📄 Master plan: `MASTER-PLAN.md`
7. ⏱️ Estimated implementation time: X days
8. 👥 Team members needed: X developers

## Error Handling

### Invalid JIRA URL/Key
If the JIRA reference cannot be parsed or accessed:
```
Error: Invalid JIRA reference.
Please provide a valid JIRA URL (https://domain.atlassian.net/browse/ISSUE-123)
or issue key (PROJ-123).
```

### Agent Failures
If any agent fails:
- Continue with other agents
- Note the failure in the master plan
- Provide partial analysis with available data

### Access Restrictions
If filesystem access is restricted:
- Use available tools (Read, Grep, Glob)
- Focus on accessible directories
- Note limitations in the final report

## Usage Examples

### Basic Usage
```
/jira-analyze https://mycompany.atlassian.net/browse/PROJ-123
```

### With Issue Key Only
```
/jira-analyze PROJ-123
```

## Notes

- All agents work sequentially in groups to build upon previous findings
- Results are saved between phases for data continuity
- Each agent has access to outputs from previous agents
- The master plan consolidates all findings into an actionable document
- All technology recommendations are backed by Context7 documentation
- Code examples follow existing project patterns