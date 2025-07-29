# Sub-Agents Guide for Claude Code

## Table of Contents
1. [What are Sub-Agents](#what-are-sub-agents)
2. [Creating Sub-Agents](#creating-sub-agents)
3. [Managing Sub-Agents](#managing-sub-agents)
4. [Using Sub-Agents](#using-sub-agents)
5. [Examples and Best Practices](#examples-and-best-practices)

## What are Sub-Agents

Sub-agents are specialized AI assistants in Claude Code that:
- Have specific purposes and expertise in particular domains
- Operate in their own context window separate from the main conversation
- Can be configured with custom system prompts and specific tool access

### Key Characteristics
- Unique name and description
- Stored as Markdown files in `.claude/agents/` (project-level) or `~/.claude/agents/` (user-level)
- Can be invoked automatically or explicitly
- Help preserve main conversation context

## Creating Sub-Agents

### Step 1: Use the `/agents` command
```bash
/agents
```

### Step 2: Define agent parameters
When creating an agent, you need to specify:
1. **Unique name** - agent identifier
2. **Detailed description** - what the agent does and when to use it
3. **Tool access** (optional) - which tools the agent can use
4. **System prompt** - specific instructions for the agent

### Agent File Structure
```markdown
---
name: code-reviewer
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability.
tools: [Read, Grep, Glob]
---

# System Prompt

You are an expert code reviewer with deep knowledge of software engineering best practices. Your role is to:

1. Analyze code for potential bugs and security vulnerabilities
2. Check for adherence to coding standards and conventions
3. Suggest improvements for readability and maintainability
4. Identify performance optimization opportunities

Always provide constructive feedback with specific examples and suggestions.
```

### File Locations
- **Project agents**: `.claude/agents/[agent-name].md`
- **User agents**: `~/.claude/agents/[agent-name].md`

## Managing Sub-Agents

### Version Control
It's recommended to add project agents to version control:
```bash
git add .claude/agents/
git commit -m "Add code-reviewer sub-agent"
```

### Updating Agents
To update an agent, edit the corresponding file:
```bash
# Edit project agent
nano .claude/agents/code-reviewer.md

# Edit user agent
nano ~/.claude/agents/debugger.md
```

### Deleting Agents
Simply remove the agent file:
```bash
rm .claude/agents/unwanted-agent.md
```

## Using Sub-Agents

### Automatic Delegation
Claude Code automatically determines when to use an appropriate agent based on context. For example:
- When errors are detected → debugger is invoked
- When code is written → code-reviewer is invoked
- When analyzing data → data-scientist is invoked

### Explicit Invocation
You can explicitly invoke an agent by mentioning its name:
```
"Use the code-reviewer to check this code"
"Let the debugger look at this error"
```

### Task Tool Usage
When using the Task tool, you must specify the agent type:
```python
Task(
    description="Review authentication code",
    prompt="Check the security of the login implementation",
    subagent_type="code-reviewer"
)
```

## Examples and Best Practices

### Example Agents

#### 1. Code Reviewer
```markdown
---
name: code-reviewer
description: Expert code review specialist. Proactively reviews code for quality, security, and maintainability.
tools: [Read, Grep, Glob, Task]
---

# Code Review Specialist

You are an expert code reviewer. Focus on:
- Security vulnerabilities
- Performance issues
- Code maintainability
- Best practices adherence
- Test coverage

Always provide actionable feedback with examples.
```

#### 2. Debugger
```markdown
---
name: debugger
description: Debugging specialist for errors, test failures, and unexpected behavior.
tools: [Read, Grep, Bash, Task]
---

# Debugging Expert

You specialize in finding and fixing bugs. Your approach:
1. Analyze error messages and stack traces
2. Identify root causes
3. Suggest specific fixes
4. Verify solutions work

Be methodical and thorough in your debugging process.
```

#### 3. Data Scientist
```markdown
---
name: data-scientist
description: Data analysis expert for SQL queries, BigQuery operations, and data insights.
tools: [Read, Write, Bash, WebSearch]
---

# Data Science Specialist

You are a data analysis expert. Help with:
- SQL query optimization
- Data visualization recommendations
- Statistical analysis
- Machine learning insights

Provide clear explanations and practical examples.
```

### Best Practices

#### 1. Create Focused Agents
- One agent = one area of responsibility
- Avoid creating "universal" agents

#### 2. Write Detailed System Prompts
- Clearly define role and objectives
- Specify concrete approaches and methodologies
- Include examples of desired behavior

#### 3. Limit Tool Access
- Grant access only to necessary tools
- This improves security and performance

#### 4. Use Version Control
- Add project agents to git
- Document changes in commit messages

#### 5. Test Your Agents
- Verify agent behavior with real tasks
- Iteratively improve prompts

### Performance Considerations
- Sub-agents help preserve main conversation context
- May add slight latency on first invocation
- Efficient for specialized tasks

### Debugging
If an agent doesn't work as expected:
1. Check YAML syntax in file header
2. Ensure agent name is unique
3. Verify access to specified tools
4. Review system prompt for clarity

## Conclusion

Sub-agents in Claude Code are a powerful tool for extending capabilities and specialization. Properly configured agents can significantly improve code work efficiency by providing expert assistance in specific domains.