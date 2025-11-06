---
description: Analyze GitHub Issue and create comprehensive implementation plan
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
    gh *: allow
---

# GitHub Issue Analysis and Implementation Planning

Analyze the GitHub Issue at: $ARGUMENTS

Please perform a comprehensive analysis following these steps:

## Phase 0: GitHub Issue Retrieval and Setup

**Extract GitHub Issue:**

1. **Parse the input $ARGUMENTS:**
   - If full URL: Extract owner/repo/number from URL pattern `https://github.com/{owner}/{repo}/issues/{number}`
   - If issue number provided: Use directly (e.g., 123) with current repository
   - Validate format before proceeding

2. **Determine repository context:**
   ```bash
   # If only number provided, get current repo from git remote
   REPO_INFO=$(git remote get-url origin | sed -E 's/.*github.com[:/]([^/]+)\/([^.]+)(\.git)?/\1\/\2/')
   # If URL provided, extract owner/repo from URL
   ```

3. **Fetch GitHub Issue details:**
   ```bash
   gh issue view <number> --repo <owner>/<repo> --json number,title,state,author,body,labels,assignees,milestone,createdAt,updatedAt
   ```

   Extract all fields:
   - **number**: Issue number
   - **title**: Issue title
   - **state**: open/closed
   - **author**: Issue creator
   - **body**: Issue description (may contain acceptance criteria)
   - **labels**: Tags and categories
   - **assignees**: Assigned developers
   - **milestone**: Target release/sprint
   - **createdAt**, **updatedAt**: Timestamps

4. **Create working directory and save issue details:**
   - Create directory: `mkdir -p working-docs/analysis/issue-[NUMBER]/`
   - Save full GitHub Issue details to: `working-docs/analysis/issue-[NUMBER]/github-issue.md`
   - Include all relevant metadata for reference
   - Parse body for acceptance criteria (typically in sections marked with ## or checklist format)

5. **Analyze issue complexity for conditional deep-dive analysis:**
   - Check for **security keywords**: authentication, authorization, encryption, security, credentials, sensitive data, tokens, API keys, permissions, access control
   - Check for **performance keywords**: scalability, high-load, optimization, caching, large-scale, performance, throughput, latency
   - Check for **complex testing keywords**: multiple integrations, critical paths, e2e flows, complex scenarios, cross-system testing
   - Set flags: `needs_security_focus`, `needs_performance_focus`, `needs_detailed_testing`
   - These flags will determine depth of analysis in later sections

## 1. Issue Analysis

- Fetch and read the GitHub Issue details carefully
- Extract key requirements from the issue body
- Identify acceptance criteria (commonly formatted as checklists or sections)
- Identify technical specifications and constraints
- Note any dependencies or blockers mentioned in comments
- Understand the business value and user impact
- Identify stakeholders and their expectations
- Review linked pull requests or related issues

## 2. Codebase Deep Dive

**Use filesystem tools for comprehensive analysis:**

### 2.1 Project Structure Overview
- Analyze the complete project directory tree and structure
- Identify key directories relevant to the issue (src, lib, components, services, etc.)
- Note the overall architecture pattern (monorepo, microservices, layered, etc.)

### 2.2 Find Related Files
- Search for files with patterns relevant to the issue
- Find configuration files (*.config.*, *.json, *.yaml, *.toml)
- Search through code for:
  - Similar implementations or related features
  - Existing patterns that should be followed
  - Components or modules that will be affected
  - Test files related to the feature area

### 2.3 Read and Analyze Relevant Files
- Read multiple related files efficiently (use batch reading when possible)
- Identify existing patterns and conventions:
  - Naming conventions
  - Code organization patterns
  - Error handling approaches
  - Logging and monitoring practices
- Note architectural decisions and design patterns in use
- Check for documentation (README files, inline comments)

### 2.4 Technology Stack Identification
- Identify frameworks, libraries, and their versions
- Check package.json, requirements.txt, pom.xml, Gemfile, etc.
- Note any custom tooling or build processes
- Identify language versions and runtime requirements

### 2.5 Integration Points
- Identify APIs, services, and databases involved
- Map data flows and dependencies
- Check for existing tests and test infrastructure
- Note any third-party integrations

### 2.6 Technical Debt Assessment
- Note deprecated patterns or libraries in use
- Identify areas needing refactoring that might impact this issue
- List potential blockers or conflicts with existing code
- Check for TODO/FIXME comments in relevant areas

## 3. Technical Research & Best Practices

**Use Context7 extensively for each technology identified:**

### 3.1 For EACH Major Technology/Library in the Issue:

**Step 1: Resolve Library ID**
- Use Context7 to resolve the library name to its proper identifier
- Example: "FastAPI" → `/tiangolo/fastapi`

**Step 2: Get Comprehensive Documentation**
- Fetch detailed documentation from Context7 with:
  - Library identifier from step 1
  - Specific topic or feature you're implementing (e.g., "async endpoints", "authentication", "state management")
  - Request substantial documentation (at least 15000 tokens) for comprehensive understanding

**Example Context7 workflow:**
```
Library: FastAPI
1. Resolve library: "FastAPI" → "/tiangolo/fastapi"
2. Get documentation:
   - Library: "/tiangolo/fastapi"
   - Topic: "async endpoints and dependency injection"
   - Documentation depth: comprehensive (15000+ tokens)
```

### 3.2 Research Best Practices
For each relevant technology, extract:
- **Implementation patterns** with pros and cons
- **Code examples** from official documentation
- **Common pitfalls** and anti-patterns to avoid
- **Performance optimization** techniques
- **Security best practices** (validation, sanitization, authorization)
- **Testing approaches** (mocking, fixtures, test data)
- **Error handling patterns** specific to the technology

### 3.3 Compare Different Approaches
- List 2-3 viable implementation strategies
- Analyze trade-offs:
  - Complexity vs maintainability
  - Performance vs readability
  - Development time vs long-term flexibility
  - Team expertise vs learning curve
- **Recommend the best approach with clear justification**

### 3.4 Check for Breaking Changes
- Verify versions of libraries currently used in the project
- Check Context7 documentation for:
  - Deprecation warnings
  - Breaking changes in recent versions
  - Migration guides if upgrades are needed
- Note any compatibility concerns

### 3.5 Industry Best Practices
- Research similar implementations in popular open-source projects
- Consider performance implications and scalability
- Look for established design patterns for this use case
- Check for relevant RFCs, specifications, or standards

## 4. Comprehensive Implementation Plan

### 4.1 Architecture & Design

**System Architecture Changes:**
- Overall architectural approach (MVC, layered, microservices, event-driven, etc.)
- Component boundaries and responsibilities
- How new components fit into existing architecture
- ASCII diagrams for component interactions (use simple box and arrow diagrams)
- Module organization and package structure

**Design Patterns:**
- Specific patterns to apply (Repository, Factory, Strategy, Observer, etc.)
- Justification for each pattern choice
- How patterns align with existing codebase conventions
- Examples of where patterns will be used

**SOLID Principles Application:**
- **Single Responsibility**: How components are separated by concerns
- **Open/Closed**: Extension points designed for future changes
- **Liskov Substitution**: Interface contracts and polymorphism
- **Interface Segregation**: Focused interfaces for different clients
- **Dependency Inversion**: Abstractions and dependency injection

### 4.2 Implementation Steps (Phased Approach)

**Phase 1: Foundation (Day 1-X)**

- [ ] **Step 1: [Foundation Task Description]**
  - **Files**: `path/to/file1.ts`, `path/to/file2.py`
  - **Changes**:
    - [Specific code changes or new components to create]
    - [Key functions/classes to add]
  - **Estimated time**: X hours
  - **Dependencies**: None
  - **Testing**: [Basic test approach - unit tests to create]

- [ ] **Step 2: [Next Foundation Task]**
  - **Files**: `path/to/file3.ts`
  - **Changes**: [Specific changes with code examples if helpful]
  - **Estimated time**: X hours
  - **Dependencies**: Step 1
  - **Testing**: [Test approach]

[Continue with more steps as needed for foundation phase]

**Phase 2: Core Features (Day X-Y)**

- [ ] **Step N: [Core Feature Task]**
  - **Files**: [List of files]
  - **Changes**: [Detailed changes]
  - **Estimated time**: X hours
  - **Dependencies**: [Previous steps]
  - **Testing**: [Integration test approach]

[Continue with core implementation steps]

**Phase 3: Integration & Polish (Day Y-Z)**

- [ ] **Step M: [Integration Task]**
  - **Files**: [List of files]
  - **Changes**: [Integration work]
  - **Estimated time**: X hours
  - **Dependencies**: [All previous core steps]
  - **Testing**: [E2E test scenarios]

[Continue with integration and polish steps]

### 4.3 Dependencies & Libraries

If new dependencies are needed:

| Library | Version | Purpose | Installation Command |
|---------|---------|---------|---------------------|
| [library-name] | [^2.0.0] | [What it does] | `npm install library-name` or `pip install library-name` |

**Justification for each new dependency:**
- Why this library is needed
- Why this library over alternatives
- License compatibility check
- Security considerations (check for known vulnerabilities)

### 4.4 Database Changes (if applicable)

**Schema Modifications:**
- New tables or collections
- Modified columns or fields
- Indexes to add for performance
- Constraints and relationships

**Migration Scripts:**
- Forward migration steps
- Data migration strategy (if data transformation needed)
- Rollback plan for each migration

**Performance Considerations:**
- Query optimization strategies
- Index strategy
- Data volume considerations

### 4.5 API Changes (if applicable)

**New Endpoints:**
```
POST /api/resource
Request: { ... }
Response: { ... }
```

**Modified Endpoints:**
- List endpoints with changes
- Breaking changes (if any)
- Backward compatibility approach

**API Versioning Strategy:**
- How breaking changes are handled
- Version strategy (URL, header, query param)

### 4.6 Performance Considerations

**Optimization Techniques:**
- **Algorithm choices**:
  - Selected algorithms with Big-O analysis
  - Why this algorithm over alternatives
- **Data structure selections**:
  - Arrays vs Sets vs Maps
  - Justification for each choice
- **Lazy loading strategies**: Where and why
- **Pagination approach**: For large datasets

**Caching Strategy:**
- **What to cache**:
  - Frequently accessed data
  - Expensive computations
  - Third-party API responses
- **Where to cache**:
  - In-memory (application level)
  - Redis/Memcached (distributed)
  - CDN (for static assets)
  - Browser cache (for client-side)
- **Cache invalidation approach**:
  - TTL strategy
  - Event-based invalidation
  - Manual invalidation triggers
- **TTL recommendations**: Specific durations with justification

**Scalability:**
- Horizontal vs vertical scaling considerations
- Load balancing approach (if applicable)
- Database query optimization strategies
- Connection pooling configuration
- Rate limiting considerations

**Performance Targets:**
- Response time targets (e.g., p95 < 200ms)
- Throughput requirements (requests/second)
- Resource utilization limits (CPU, memory)

### 4.7 CI/CD Updates

**Build Process Modifications:**
- Changes to build scripts or configuration
- New build steps required
- Build optimization opportunities

**New Test Stages or Quality Gates:**
- Additional test stages in pipeline
- New quality gates (coverage thresholds, performance benchmarks)
- Static analysis or linting rules

**Deployment Strategy:**
- Blue-green deployment considerations
- Canary release approach
- Feature flags for gradual rollout
- Database migration timing in deployment

**Environment-Specific Configurations:**
- Development environment setup
- Staging environment requirements
- Production environment considerations
- Environment variables needed

**Rollback Procedures:**
- How to rollback if deployment fails
- Database rollback strategy
- Feature flag disable procedure

## 5. Risk Assessment & Mitigation

### 5.1 Risk Matrix

Identify and categorize all potential risks:

| Risk | Probability | Impact | Severity | Mitigation Strategy |
|------|------------|--------|----------|---------------------|
| [Risk description] | Low/Med/High | Low/Med/High | Critical/High/Medium/Low | [Specific actionable mitigation] |

**Example risks to consider:**
- Third-party API dependencies and reliability
- Data migration complexity and volume
- Breaking changes impact on existing functionality
- Team knowledge gaps for new technologies
- Timeline and resource constraints
- Environment differences (dev/staging/prod)

### 5.2 Conditional Deep Dives

**IF `needs_security_focus` flag is TRUE:**

#### Security Threat Analysis

**Potential Vulnerabilities:**
- **Authentication/Authorization issues**:
  - Broken authentication mechanisms
  - Missing authorization checks
  - Privilege escalation possibilities
  - Session management weaknesses

- **Data exposure risks**:
  - Sensitive data in logs or error messages
  - Unencrypted data transmission
  - Inadequate data masking
  - Information disclosure vulnerabilities

- **Injection attacks**:
  - SQL injection vectors
  - XSS (Cross-Site Scripting) possibilities
  - CSRF (Cross-Site Request Forgery) risks
  - Command injection opportunities
  - NoSQL injection (if applicable)

- **API security**:
  - Missing rate limiting
  - Lack of input validation
  - Insecure direct object references
  - Mass assignment vulnerabilities

**Security Controls to Implement:**
- **Input validation requirements**:
  - Validation rules for each input field
  - Whitelist approach for allowed values
  - Type checking and format validation
  - Length and size limits

- **Encryption needs**:
  - Data at rest: What data needs encryption (PII, credentials, etc.)
  - Data in transit: TLS/SSL requirements
  - Encryption algorithms and key management

- **Audit logging requirements**:
  - What events to log (authentication, authorization, data access)
  - Log format and retention policy
  - PII handling in logs
  - Monitoring and alerting setup

- **Compliance considerations**:
  - GDPR requirements (if handling EU user data)
  - HIPAA requirements (if healthcare data)
  - PCI DSS (if payment data)
  - Other industry-specific regulations

**Security Testing Plan:**
- OWASP Top 10 testing checklist
- Penetration testing scenarios
- Security code review focus areas
- Automated security scanning tools to use

---

**IF `needs_performance_focus` flag is TRUE:**

#### Performance Analysis

**Performance Risks:**
- **Bottleneck identification**:
  - Database query performance
  - N+1 query problems
  - Slow API endpoints
  - Heavy computation blocks
  - Large payload transfers

- **Resource consumption**:
  - CPU-intensive operations
  - Memory leaks or high memory usage
  - Network bandwidth constraints
  - Disk I/O bottlenecks

- **Third-party dependencies**:
  - External API latency
  - Third-party service reliability
  - Timeout handling
  - Circuit breaker needs

**Performance Optimization Plan:**
- **Load testing strategy**:
  - Load testing tools to use (k6, JMeter, Gatling)
  - Test scenarios and user flows
  - Expected load (concurrent users, requests/second)
  - Acceptance criteria for performance

- **Performance monitoring**:
  - Metrics to track (response time, throughput, error rate)
  - APM tool integration (New Relic, DataDog, etc.)
  - Alerting thresholds
  - Dashboard setup

- **Circuit breakers and timeouts**:
  - Where to implement circuit breakers
  - Timeout values for different operations
  - Fallback strategies
  - Retry policies with exponential backoff

- **Graceful degradation**:
  - Feature prioritization under load
  - Degraded mode functionality
  - User communication strategy

**Performance Benchmarks:**
- Response time targets: p50, p95, p99
- Throughput requirements
- Resource utilization limits
- Error rate thresholds

---

**IF `needs_detailed_testing` flag is TRUE:**

#### Comprehensive Testing Strategy

**Testing Challenges:**
- **Complex integration scenarios**:
  - Multiple system interactions
  - Asynchronous workflows
  - External service dependencies
  - State management across services

- **Test data management**:
  - Test data generation strategy
  - Data cleanup between tests
  - Handling sensitive data in tests
  - Test database setup and teardown

- **Environment setup complexity**:
  - Local development environment
  - CI/CD environment configuration
  - Mock vs real services decision
  - Docker/containerization for consistency

- **Flaky test prevention**:
  - Race condition handling
  - Proper wait strategies
  - Idempotent test design
  - Test isolation

**Detailed Testing Plan:**

**Unit Tests:**
- Coverage target: >80% for new code
- Specific components to test:
  - [List components with test scenarios]
- Mocking strategy:
  - What to mock (external dependencies, databases)
  - Mocking frameworks to use
- Test file organization and naming conventions

**Integration Tests:**
- Key integration scenarios:
  - [List specific integration paths]
- API contract testing approach
- Database integration test strategy
- Message queue/event testing (if applicable)

**E2E Tests:**
- Critical user flows to test:
  - [List end-to-end scenarios]
- E2E testing framework (Playwright, Cypress, Selenium)
- Test data setup and cleanup
- Screenshot/video capture on failure

**Performance Tests:**
- Load test scenarios
- Stress test conditions
- Soak test duration
- Benchmark comparisons

**Security Tests:**
- Automated security scanning
- Manual security test cases
- Penetration test scenarios

**Test Execution Plan:**
- When each test type runs (on commit, PR, merge, nightly)
- Parallel execution strategy
- Test reporting and notifications
- Flaky test handling process

### 5.3 General Risks

**Backward Compatibility:**
- Breaking changes identification
- Migration path for existing users
- Deprecation strategy
- Communication plan

**Third-Party Dependencies:**
- Dependency reliability assessment
- Vendor lock-in considerations
- Alternative options if primary fails
- License compatibility

**Team Knowledge Gaps:**
- Technologies new to the team
- Training or documentation needed
- Pair programming opportunities
- External expertise requirements

**Timeline and Resource Constraints:**
- Critical path identification
- Resource allocation
- Buffer for unknowns
- Scope reduction options if needed

## 6. Executive Summary & Deliverables

### 6.1 Compile Implementation Plan Document

**Create comprehensive plan file:**
Save the complete analysis to: `working-docs/analysis/issue-[NUMBER]/IMPLEMENTATION-PLAN.md`

**Structure of IMPLEMENTATION-PLAN.md:**

```markdown
# Implementation Plan: Issue #[NUMBER] - [Issue Title]

## Executive Summary

- **Issue overview**: [Brief description and business value]
- **Key technologies**: [List of technologies/frameworks/libraries]
- **Estimated timeline**: X days total
  - Foundation: X days
  - Core Implementation: X days
  - Integration & Testing: X days
  - Deployment: X days
- **Risk level**: [Low/Medium/High]
- **Team size needed**: X developers
- **Complexity score**: [1-10 with explanation]

## 1. Issue Requirements

[Summary from Phase 0 - github-issue.md]

**Acceptance Criteria:**
- [List all acceptance criteria from GitHub Issue body]

**Dependencies:**
- [List any dependent issues or blockers]

## 2. Current State Analysis

[From Section 2 - Codebase Deep Dive]

**Affected Components:**
- [List files and modules that will be modified]

**Existing Patterns to Follow:**
- [Note coding conventions and patterns found]

**Technology Stack:**
- [List current technologies relevant to this issue]

**Technical Debt Considerations:**
- [Any technical debt that impacts this work]

## 3. Architecture & Design

[From Section 4.1]

**System Architecture:**
[Architecture diagram and description]

**Design Patterns:**
[Patterns to be used with justification]

**Component Design:**
[Detailed component breakdown]

## 4. Best Practices Research

[From Section 3 - Key findings from Context7]

**Recommended Approach:**
[The chosen implementation strategy with justification]

**Code Examples from Documentation:**
[Relevant examples from Context7 research]

**Anti-Patterns to Avoid:**
[List of things NOT to do]

## 5. Implementation Steps

[From Section 4.2 - All phases with detailed steps]

### Phase 1: Foundation
[Detailed steps with file paths, time estimates, dependencies]

### Phase 2: Core Features
[Detailed steps]

### Phase 3: Integration & Polish
[Detailed steps]

## 6. Dependencies & Database Changes

[From Sections 4.3 and 4.4]

**New Dependencies:**
[Table of dependencies]

**Database Migrations:**
[Migration details if applicable]

## 7. API Changes

[From Section 4.5 if applicable]

**Endpoints:**
[List of API changes]

## 8. Testing Strategy

[From Section 5.2 if detailed testing needed, or Section 4.2 basic testing]

**Unit Tests:**
- X new test files required
- Coverage target: >80%

**Integration Tests:**
- Y integration scenarios

**E2E Tests:**
- Z critical user flows

**Test Execution:**
[When and how tests run]

## 9. Security Measures

[From Section 5.2 if security focus needed]

**Security Controls:**
[List of security implementations]

**Threat Mitigation:**
[How identified threats are addressed]

**Compliance:**
[Any compliance requirements]

## 10. Performance Optimizations

[From Section 4.6]

**Optimization Techniques:**
[Algorithm and data structure choices]

**Caching Strategy:**
[What, where, and how to cache]

**Performance Targets:**
[Specific metrics and goals]

**Monitoring:**
[How performance is tracked]

## 11. CI/CD Considerations

[From Section 4.7]

**Pipeline Modifications:**
[Changes to CI/CD pipeline]

**Deployment Strategy:**
[How this will be deployed]

**Rollback Procedures:**
[How to rollback if needed]

## 12. Risk Assessment

[From Section 5 - Risk Matrix and detailed analysis]

### Risk Matrix

| Risk | Probability | Impact | Severity | Mitigation |
|------|------------|--------|----------|------------|
| [All identified risks with mitigations] |

### Detailed Risk Analysis

[Expanded discussion of critical risks]

## 13. Success Criteria Checklist

Track implementation progress:

- [ ] All acceptance criteria from GitHub Issue met
- [ ] Unit tests written and passing (>80% coverage)
- [ ] Integration tests passing
- [ ] E2E tests for critical paths passing
- [ ] Performance benchmarks met (response times, throughput)
- [ ] Security scan passed (no critical vulnerabilities)
- [ ] Code review completed and approved
- [ ] Documentation updated (README, API docs, inline comments)
- [ ] Deployed to staging environment successfully
- [ ] QA testing completed and sign-off received
- [ ] Production deployment completed
- [ ] Post-deployment monitoring verified

## 14. Timeline & Milestones

| Phase | Description | Duration | Dependencies | Status | Assignee |
|-------|------------|----------|--------------|--------|----------|
| 1 | Foundation | X days | None | Not Started | TBD |
| 2 | Core Implementation | X days | Phase 1 | Not Started | TBD |
| 3 | Integration & Testing | X days | Phase 2 | Not Started | TBD |
| 4 | Deployment | X days | Phase 3 | Not Started | TBD |

**Total Estimated Time: X days**

**Milestones:**
- End of Phase 1: Foundation complete, basic structure in place
- End of Phase 2: Core features implemented and unit tested
- End of Phase 3: Fully integrated, all tests passing
- End of Phase 4: Deployed to production and verified

## 15. Next Steps

Immediate actions to take:

1. [ ] Review this implementation plan with the team
2. [ ] Get stakeholder approval and sign-off
3. [ ] Create GitHub Issue tasks/subtasks for each phase/step
4. [ ] Assign developers to tasks based on expertise
5. [ ] Schedule kickoff meeting with all stakeholders
6. [ ] Set up development environment and branch
7. [ ] Begin Phase 1 implementation
8. [ ] Schedule regular progress check-ins (daily standups, weekly reviews)

## 16. Questions & Clarifications

[List any questions that need answers before starting implementation]

- [Question 1]
- [Question 2]

## 17. References

- GitHub Issue: [Link to issue]
- Related Documentation: [Links to relevant docs]
- Context7 Research: [Note which libraries were researched]
- Similar Implementations: [Links to reference code]
```

### 6.2 Console Summary

After saving the IMPLEMENTATION-PLAN.md file, provide a concise summary to the user with:

- **Issue overview**: Type, complexity, affected components, technologies, estimated time
- **Key findings**: Most important insights from the analysis
- **Critical risks**: Highlight any high or critical risks identified
- **Deliverables**: Paths to generated files (IMPLEMENTATION-PLAN.md, github-issue.md)
- **Readiness assessment**: Ready/Partially Ready/Not Ready with brief explanation
- **Questions for team**: Any clarifications needed before implementation
- **Recommended next steps**: Priority actions to take

## Usage Notes

- **Extended thinking mode** is enabled to provide deeper analysis
- **Comprehensive tool access** allows thorough codebase exploration
- **Conditional deep dives** provide detailed analysis for complex areas
- **Structured deliverables** create reusable implementation artifacts

Focus on practical, actionable insights that the team can use immediately.

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

### Access Restrictions
If you don't have access to the repository or issue:
```
❌ Error: Access denied to GitHub Issue #123.
Please ensure you have read access to the repository and the issue exists.
```

## Usage Examples

### Basic Usage with Issue Number
```
/github-issue-analyze 123
```

### With Full GitHub URL
```
/github-issue-analyze https://github.com/owner/repo/issues/456
```
