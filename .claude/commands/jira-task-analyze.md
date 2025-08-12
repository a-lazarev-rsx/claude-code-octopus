---
description: Analyze JIRA task and create comprehensive implementation plan
allowed-tools: mcp__atlassian, Read, Grep, Bash, mcp__context7
---

# JIRA Task Analysis and Implementation Planning

Analyze the JIRA task at: $ARGUMENTS

Please perform a comprehensive analysis following these steps:

## 1. Task Analysis
- Fetch and read the JIRA task details carefully
- Extract key requirements and acceptance criteria
- Identify technical specifications and constraints
- Note any dependencies or blockers mentioned

## 2. Codebase Assessment 
- Examine the current project structure
- Identify existing components that relate to this task
- Check for similar implementations or patterns in the codebase
- Evaluate if current architecture supports the required changes
- List any technical debt that might impact implementation

## 3. Technical Research
- Use MCP context7 to search for best practices and solutions
- Look for relevant libraries, frameworks, or patterns
- Research similar implementations in popular open-source projects
- Consider performance implications and scalability

## 4. Implementation Plan
Create a detailed step-by-step plan including:
- File modifications needed (list specific files and changes)
- New files/components to create
- Required dependencies or libraries to add
- Database changes if applicable
- API endpoints or interface changes
- Testing strategy (unit tests, integration tests)
- Estimated time for each step

## 5. Risk Assessment
- Identify potential risks or challenges
- Suggest mitigation strategies
- Highlight areas needing special attention
- Consider backward compatibility issues

## 6. Summary
Provide a concise summary with:
- Overall readiness assessment (Ready/Partially Ready/Not Ready)
- Critical path items
- Recommended approach
- Any questions needing clarification from the team

Format the response with clear headers and bullet points for easy reading.
Focus on practical, actionable insights.