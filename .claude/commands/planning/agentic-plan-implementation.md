---
description: Comprehensive implementation planning with Context7 research, architecture analysis, and CI/CD planning
argument-hint: "task description or requirements"
allowed-tools: Task, Read, Grep, Glob, WebSearch, mcp__context7__*, Bash, LS
model: opus
---

# Implementation Planning with Context7 and Best Practices

Analyze the codebase, research documentation with Context7, and create a comprehensive implementation plan including testing, CI/CD, and documentation.

## Instructions:

### Phase 1: Task Analysis and Comprehensive Codebase Research

1. **Parse the task description**: $ARGUMENTS

2. **Comprehensive Project Analysis**:
   - Identify project type and overall structure
   - Detect all package managers and build tools:
     - JavaScript/TypeScript: package.json, yarn.lock, pnpm-lock.yaml
     - Python: requirements.txt, Pipfile, pyproject.toml, setup.py
     - Java: pom.xml, build.gradle, build.sbt
     - .NET: *.csproj, *.sln, packages.config
     - Go: go.mod, go.sum
     - Rust: Cargo.toml, Cargo.lock
     - Ruby: Gemfile, Gemfile.lock
   
3. **Technology Stack Deep Dive**:
   - Frontend frameworks (React, Vue, Angular, Svelte)
   - Backend frameworks (Express, Django, Spring, .NET Core)
   - Database systems (PostgreSQL, MySQL, MongoDB, Redis)
   - Message queues (RabbitMQ, Kafka, AWS SQS)
   - Authentication methods (JWT, OAuth, SAML)
   - API patterns (REST, GraphQL, gRPC)
   - Testing frameworks and coverage tools
   - CI/CD configurations (Jenkins, GitHub Actions, GitLab CI)
   - Container and orchestration (Docker, Kubernetes)
   - Cloud services (AWS, GCP, Azure)
   - Monitoring and logging tools
   - Code quality tools (linters, formatters)

### Phase 2: Parallel Agent Execution

Launch specialized agents to analyze different aspects:

```python
# 1. Architecture and Quality Planning
Task(
  description="Plan architecture and quality",
  subagent_type="planning-quality-advisor",
  prompt=f"""
  Design the architecture for the following implementation task:
  {task_description}
  
  Project structure:
  {project_structure}
  
  Focus on:
  1. Overall system architecture design
  2. Component structure and boundaries
  3. Design patterns appropriate for this feature
  4. Consistency with existing architecture
  5. SOLID principles application
  6. Module organization and file structure
  
  Provide:
  - Architecture diagrams (ASCII or descriptions)
  - Component responsibilities
  - Interface definitions
  - Quality guidelines for the implementation
  """
)

# 2. Best Practices Research with Context7
Task(
  description="Research best practices",
  subagent_type="planning-best-practices",
  prompt=f"""
  Research comprehensive best practices for:
  {task_description}
  
  Technologies identified:
  {technologies}
  
  MANDATORY REQUIREMENTS:
  1. Use mcp__context7__resolve-library-id for EVERY technology/framework
  2. Use mcp__context7__get-library-docs to get extensive documentation (15000+ tokens)
  3. Research multiple implementation patterns for each technology
  4. Compare different approaches with pros/cons
  5. Find code examples and patterns from official docs
  6. Check for breaking changes and deprecations
  7. Research performance optimization techniques
  8. Find security best practices
  
  Research topics for each technology:
  - Official recommended patterns
  - Common anti-patterns to avoid
  - Performance benchmarks
  - Security guidelines
  - Testing approaches
  - Migration guides
  - Version compatibility
  
  Output:
  - Comprehensive comparison of approaches
  - Actual code examples from Context7
  - Performance implications
  - Security considerations
  - Clear recommendations with evidence
  """
)

# 3. Architecture and Implementation Planning
Task(
  description="Create implementation architecture",
  subagent_type="planning-implementation",
  prompt=f"""
  Create detailed implementation architecture for:
  {task_description}
  
  Based on research from best-practices-checker.
  
  Design:
  1. System architecture (layers, services, components)
  2. Database schema and data models
  3. API contracts and interfaces
  4. State management architecture
  5. Deployment topology
  6. Infrastructure requirements
  
  Create:
  - Architecture diagrams
  - Component interaction flows
  - Implementation phases with milestones
  - Deployment architecture
  - Risk assessment matrix
  
  Focus on ARCHITECTURE and STRUCTURE, not documentation research.
  Provide concrete implementation roadmap with clear phases.
  """
)

# 4. CI/CD Pipeline Planning
Task(
  description="Plan CI/CD changes",
  subagent_type="planning-ci-cd",
  prompt=f"""
  Analyze and plan CI/CD pipeline updates for:
  {task_description}
  
  Current CI/CD setup:
  - Check for Jenkinsfile, .github/workflows/, .gitlab-ci.yml
  - Identify existing stages and jobs
  - Find test execution patterns
  
  Plan pipeline updates including:
  1. New test stages needed
  2. Build process modifications
  3. Quality gates to add
  4. Deployment changes
  5. Monitoring and rollback procedures
  
  Focus on Jenkins if Jenkinsfile exists, including:
  - Pipeline as Code improvements
  - Shared library usage
  - Parallel execution opportunities
  - Resource optimization
  
  Provide specific Jenkinsfile/workflow examples.
  """
)

# 5. Testing Strategy Planning
Task(
  description="Plan comprehensive testing",
  subagent_type="planning-testing-strategist",
  prompt=f"""
  Design comprehensive testing strategy for:
  {task_description}
  
  Current testing setup:
  {test_frameworks}
  
  Plan:
  1. Test pyramid structure (unit, integration, e2e ratios)
  2. Test scenarios and use cases
  3. Test data management strategy
  4. Mock and stub strategies
  5. Performance and load testing approach
  6. Security testing requirements
  
  Provide:
  - Detailed test plans for each component
  - Test coverage targets
  - Test automation strategy
  - CI/CD integration approach
  - Specific test examples with AAA pattern
  - Test data fixtures and factories
  """
)

# 6. Documentation Planning
Task(
  description="Plan documentation",
  subagent_type="planning-documentation",
  prompt=f"""
  Plan documentation updates for:
  {task_description}
  
  Analyze existing documentation:
  - README files structure
  - API documentation format
  - Code comment standards
  - User guides
  
  Plan documentation updates:
  1. README sections to add/modify
  2. API documentation for new endpoints
  3. Code comments and docstrings
  4. Architecture diagrams
  5. Configuration documentation
  6. Troubleshooting guides
  
  Provide specific examples following project conventions.
  Include documentation automation recommendations.
  """
)

# 7. Security Architecture Planning
Task(
  description="Design security architecture",
  subagent_type="planning-security-architect",
  prompt=f"""
  Design security architecture for:
  {task_description}
  
  Plan:
  1. Authentication and authorization architecture
  2. Data protection and encryption strategy
  3. Input validation and sanitization approach
  4. Security headers and configurations
  5. Audit and logging strategy
  6. Compliance requirements (GDPR, HIPAA, etc.)
  
  Perform threat modeling:
  - STRIDE analysis
  - Attack vectors identification
  - Risk assessment
  - Mitigation strategies
  
  Provide:
  - Security architecture overview
  - Authentication/authorization flow
  - Data protection matrix
  - Security checklist
  - Incident response plan
  """
)

# 8. Performance Architecture Planning
Task(
  description="Design performance architecture",
  subagent_type="planning-performance-architect",
  prompt=f"""
  Design high-performance architecture for:
  {task_description}
  
  Performance requirements:
  - Expected load and concurrent users
  - Response time targets
  - Throughput requirements
  - Data volume projections
  
  Design:
  1. Optimal algorithms and data structures
  2. Multi-layer caching strategy
  3. Database optimization approach
  4. Load distribution patterns
  5. Async processing architecture
  6. Scalability design (horizontal/vertical)
  
  Provide:
  - Performance architecture overview
  - Caching strategy matrix
  - Database optimization plan
  - Load testing scenarios
  - Monitoring and metrics plan
  - Performance optimization checklist
  """
)

# 9. Bug Prevention Planning
Task(
  description="Plan bug prevention strategies",
  subagent_type="planning-bug-prevention",
  prompt=f"""
  Design bug prevention strategies for:
  {task_description}
  
  Identify and plan for:
  1. Edge cases and boundary conditions
  2. Error scenarios and recovery
  3. Race conditions and concurrency issues
  4. Resource management (memory, connections)
  5. State management issues
  6. Integration failure points
  
  Design:
  - Defensive programming strategies
  - Input validation hierarchy
  - Error handling patterns
  - State machine design
  - Resource management approach
  - Recovery and rollback procedures
  
  Provide:
  - Edge case analysis matrix
  - Error handling strategy
  - Defensive code patterns
  - Testing scenarios for reliability
  """
)
```

### Phase 3: Web Research for Latest Practices

Perform web searches for current best practices:
- "{main_technology} best practices 2024"
- "{task_type} implementation guide"
- "{technology} performance optimization"
- "{technology} security considerations"

### Phase 4: Compile Comprehensive Plan

Aggregate all agent results into a unified implementation plan:

```markdown
# Implementation Plan: [Task Name]

## Executive Summary
- Task overview
- Key technologies
- Estimated timeline
- Risk assessment

## 1. Architecture & Design
[From planning-implementation and planning-quality-advisor]
- Current architecture analysis
- Proposed changes
- Design patterns to use
- Integration strategy

## 2. Implementation Steps
[From planning-implementation with Context7 examples]

### Phase 1: Foundation (X hours)
#### Step 1.1: [Component Name]
**Files to modify:**
- `src/components/NewComponent.js`

**Implementation:**
\`\`\`javascript
// Example from Context7 React documentation
import React from 'react';

const NewComponent = () => {
  // Implementation details with best practices
};
\`\`\`

**Testing:**
\`\`\`javascript
// Unit test example
describe('NewComponent', () => {
  it('should render correctly', () => {
    // Test implementation
  });
});
\`\`\`

### Phase 2: Core Features (Y hours)
[Continue with detailed steps...]

## 3. Testing Strategy
[From planning-testing-strategist]
- Unit tests: X new tests
- Integration tests: Y scenarios
- E2E tests: Z user flows
- Coverage target: N%

## 4. CI/CD Pipeline Updates
[From planning-ci-cd]

### Jenkinsfile Changes:
\`\`\`groovy
pipeline {
    stages {
        stage('New Tests') {
            steps {
                // Pipeline updates
            }
        }
    }
}
\`\`\`

## 5. Documentation Plan
[From planning-documentation]
- README updates
- API documentation
- User guides
- Migration guides

## 6. Security Considerations
[From planning-security-architect]
- Security measures implemented
- Vulnerability mitigations
- Compliance requirements

## 7. Performance Optimizations
[From planning-performance-architect]
- Optimization strategies
- Caching implementation
- Monitoring setup

## 8. Risk Mitigation
[From planning-bug-prevention and planning-implementation]
- Identified risks
- Mitigation strategies
- Rollback procedures
- Monitoring requirements

## 9. Timeline & Milestones
| Phase | Description | Duration | Dependencies |
|-------|------------|----------|--------------|
| 1 | Foundation | 2 days | None |
| 2 | Core Implementation | 3 days | Phase 1 |
| 3 | Testing | 2 days | Phase 2 |
| 4 | Documentation | 1 day | Phase 3 |
| 5 | CI/CD Setup | 1 day | Phase 3 |
| 6 | Deployment | 0.5 days | All phases |

**Total Estimated Time:** X days

## 10. Success Criteria
- All tests passing (100% pass rate)
- Code coverage > X%
- Performance benchmarks met
- Security scan passed
- Documentation complete
- CI/CD pipeline updated
- Deployed to staging

## 11. Follow-up Tasks
- Monitor performance metrics
- Gather user feedback
- Plan optimization iterations
- Schedule security review

## Appendix: Context7 Documentation References
[List of all Context7 and web resources used]
```

### Phase 5: Final Validation

Review the plan for:
1. Completeness - all aspects covered
2. Feasibility - realistic estimates
3. Consistency - follows project patterns
4. Quality - includes best practices
5. Clarity - easy to follow

## Output Format:

Present the comprehensive implementation plan with:
- Clear phases and milestones
- Specific code examples from Context7
- Detailed testing approach
- CI/CD pipeline updates (especially Jenkins)
- Documentation requirements
- Risk assessment and mitigation
- Time estimates for each component
- Success criteria and metrics

Remember: Every technology recommendation MUST be backed by Context7 documentation or web research. Include specific, runnable code examples that follow the project's existing patterns.