# Sub-Agents Guide for Claude Code

> **Official Documentation**: [Claude Code Sub-Agents](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
> [Common Workflows](https://docs.anthropic.com/en/docs/claude-code/common-workflows)

## Table of Contents
1. [What are Sub-Agents](#what-are-sub-agents)
2. [Creating Sub-Agents](#creating-sub-agents)
3. [Managing Sub-Agents](#managing-sub-agents)
4. [Using Sub-Agents](#using-sub-agents)
5. [Hook System](#hook-system)
6. [Integration with MCP](#integration-with-mcp)
7. [Examples and Best Practices](#examples-and-best-practices)
8. [Advanced Use Cases](#advanced-use-cases)

## What are Sub-Agents

Sub-agents are specialized AI assistants in Claude Code that:
- Have specific purposes and expertise in particular domains
- Operate in separate context windows from the main conversation, helping preserve context
- Can be configured with custom system prompts and specific tool access
- Enable automatic delegation based on task requirements
- Are stored as Markdown files with YAML frontmatter for configuration

### Key Characteristics
- Unique name and description
- Stored as Markdown files in `.claude/agents/` (project-level) or `~/.claude/agents/` (user-level)
- Can be invoked automatically or explicitly
- Help preserve main conversation context
- Support both restricted and full tool access configurations

## Creating Sub-Agents

### Step 1: Use the `/agents` command
```bash
/agents
```
This opens the subagent interface where you can list existing agents and create new ones.

### Step 2: Define agent parameters
When creating an agent, you need to specify:
1. **Unique name** - agent identifier (case-sensitive)
2. **Detailed description** - what the agent does and when to use it (enables automatic delegation)
3. **Tool access** (optional) - which tools the agent can use (restrict for security and performance)
4. **System prompt** - specific instructions for the agent's role and approach

### Agent File Structure
```markdown
---
name: agent-identifier
description: Clear description of agent's purpose and when it should be used
tools: [Read, Grep, Glob] # Optional - if not specified, inherits all tools
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
Claude Code automatically delegates to appropriate agents based on:
- Task requirements matching agent descriptions
- Context analysis of the current request
- Agent capabilities and tool access
- The description field is crucial for enabling automatic delegation

Examples:
- When errors are detected → debugger is invoked
- When code is written → code-reviewer is invoked
- When analyzing data → data-scientist is invoked

### Explicit Invocation
You can explicitly invoke an agent by mentioning its name:
```
"Use the code-reviewer to check this code"
"Let the debugger look at this error"
"Have the api-designer create the endpoints"
```

### Task Tool Usage
When using the Task tool programmatically, specify the subagent_type parameter:
```python
Task(
    description="Review authentication code",
    prompt="Check the security of the login implementation",
    subagent_type="code-reviewer"  # Must match agent name exactly
)
```

### Command-Line Options
- `/agents` - Open agent management interface
- `claude --continue` - Resume previous conversations
- `claude --print` with `--continue` - Non-interactive mode for automated workflows
- Agents are accessible in both interactive and SDK modes

## Hook System

The Hook System allows customization of agent behavior through pre and post-processing hooks.

### Hook Configuration
Hooks can be configured in `settings.json` with the following parameters:
- **`matcher`** - Pattern-based tool name matching (case-sensitive)
- **`timeout`** - Command execution time limits in seconds
- **`command`** - Shell command to execute

### Hook Types
1. **Pre-processing hooks** - Run before tool execution
2. **Post-processing hooks** - Run after tool completion
3. **User prompt hooks** - Intercept and modify user inputs

### Example Hook Configuration
```json
{
  "hooks": {
    "pre-tool": {
      "matcher": "Write|Edit",
      "command": "echo 'About to modify file: {file_path}'",
      "timeout": 5
    }
  }
}
```

## Integration with MCP

Model Context Protocol (MCP) is an open-source standard that enables connection to external tools and services. Sub-agents can leverage MCP servers for enhanced capabilities.

### Supported MCP Integrations
- **Development Tools**: GitHub, Sentry, Linear
- **Documentation**: Notion, Confluence
- **Databases**: PostgreSQL, MongoDB, SQLite
- **Cloud Services**: AWS, Google Drive
- **Communication**: Slack, Discord

### Adding MCP Servers
```bash
# Using CLI
claude mcp add --transport stdio github npx @modelcontextprotocol/server-github

# Or via settings.json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"  # Uses environment variable
      }
    }
  }
}
```

### MCP with Sub-Agents
Sub-agents can leverage MCP tools for enhanced capabilities:
- Access external data sources
- Integrate with third-party APIs
- Utilize specialized services

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

#### 4. API Designer
```markdown
---
name: api-designer
description: API design and implementation specialist for RESTful and GraphQL services.
tools: [Read, Write, Edit, Grep]
---

# API Design Expert

You specialize in API design and implementation:
- Design RESTful endpoints following best practices
- Create OpenAPI/Swagger specifications
- Implement GraphQL schemas
- Ensure proper authentication and rate limiting

Follow REST principles and industry standards.
```

#### 5. Performance Optimizer
```markdown
---
name: performance-optimizer
description: Performance analysis and optimization specialist.
tools: [Read, Bash, Grep, Edit]
---

# Performance Optimization Expert

Focus on identifying and resolving performance bottlenecks:
- Analyze code for inefficiencies
- Profile application performance
- Optimize database queries
- Implement caching strategies
- Reduce bundle sizes

Provide measurable improvements with benchmarks.
```

### Best Practices

#### 1. Create Focused Agents
- One agent = one area of responsibility
- Avoid creating "universal" agents
- Use descriptive names that clearly indicate purpose

#### 2. Write Detailed System Prompts
- Clearly define role and objectives
- Specify concrete approaches and methodologies
- Include examples of desired behavior

#### 3. Limit Tool Access
- Grant access only to necessary tools
- This improves both security and performance
- Use the principle of least privilege
- Consider read-only tools for review agents

#### 4. Use Version Control
- Add project agents to git
- Document changes in commit messages

#### 5. Test Your Agents
- Verify agent behavior with real tasks
- Iteratively improve prompts

### Performance Considerations
- Sub-agents help preserve main conversation context by operating in separate windows
- May add slight latency on first invocation while loading agent configuration
- More efficient for specialized tasks than using the main agent
- Tool restrictions can improve response time

### Debugging
If an agent doesn't work as expected:
1. Check YAML syntax in file header (must be valid YAML)
2. Ensure agent name is unique and contains no special characters
3. Verify access to specified tools in settings
4. Review system prompt for clarity and specificity
5. Use `claude --debug` to see detailed execution logs
6. Check file location (`.claude/agents/` or `~/.claude/agents/`)

## Advanced Use Cases

### 1. SRE Incident Response
Create specialized agents for site reliability engineering:
```markdown
---
name: sre-incident-responder
description: Handles production incidents, analyzes logs, and implements fixes.
tools: [Bash, Read, Grep, WebFetch]
---

# SRE Incident Response Specialist

Respond to production incidents by:
1. Analyzing error logs and metrics
2. Identifying root causes
3. Implementing immediate fixes
4. Creating incident reports
5. Suggesting prevention measures
```

### 2. Security Audit Agent
For automated security reviews:
```markdown
---
name: security-auditor
description: Performs security audits on code and infrastructure.
tools: [Read, Grep, Bash, WebSearch]
---

# Security Audit Expert

Conduct thorough security reviews:
- Check for OWASP Top 10 vulnerabilities
- Analyze dependencies for known CVEs
- Review authentication and authorization
- Identify sensitive data exposure
- Suggest security improvements
```

### 3. Legal Document Reviewer
For compliance and legal requirements:
```markdown
---
name: legal-reviewer
description: Reviews code for licensing and compliance issues.
tools: [Read, Grep, WebSearch]
---

# Legal Compliance Specialist

Review code for:
- License compatibility
- Copyright compliance
- Privacy regulations (GDPR, CCPA)
- Export control requirements
- Third-party attribution
```

### 4. Team-Specific Workflows

#### Creating Team Standards Agent
```markdown
---
name: team-standards
description: Enforces team-specific coding standards and practices.
tools: [Read, Edit, Grep]
---

# Team Standards Enforcer

Ensure code follows team guidelines:
- Naming conventions
- File organization
- Documentation requirements
- Testing standards
- Git commit message format
```

### 5. Multi-Agent Collaboration

Agents can work together for complex tasks:

```bash
# Sequential agent collaboration
"Use the api-designer to create the endpoints, then have the code-reviewer check them"

# Parallel agent work
"Have the debugger fix the errors while the performance-optimizer analyzes the bottlenecks"
```

### 6. CI/CD Integration

Integrate agents with continuous integration:
```yaml
# .github/workflows/code-review.yml
name: Automated Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Claude Code Review
        run: |
          claude-code --continue --print "Use code-reviewer to analyze changes"
```

### 7. Domain-Specific Agents

Create agents for specific domains:
- **Machine Learning**: Model training and evaluation
- **DevOps**: Infrastructure as code management
- **Mobile Development**: iOS/Android specific reviews
- **Game Development**: Performance and gameplay optimization
- **Blockchain**: Smart contract auditing

## Configuration and Settings

### Settings Hierarchy
1. Enterprise managed policies (highest priority)
2. User settings (`~/.claude/settings.json`)
3. Project settings (`.claude/settings.json`)
4. Local settings (`.claude/settings.local.json`)

### Agent-Related Configuration
```json
{
  "permissions": {
    "allow": ["Task"],  // Allow agent invocation
    "deny": [],
    "ask": []
  },
  "env": {
    "CLAUDE_PROJECT_DIR": "/path/to/project"
  }
}
```

### Team Configuration Rollout
For team-wide agent deployment:
1. Create shared agents in version control
2. Document agent purposes and usage
3. Set up automated agent updates
4. Monitor agent performance and usage

## Troubleshooting

### Common Issues and Solutions

1. **Agent Not Found**
   - Check file location (`.claude/agents/` or `~/.claude/agents/`)
   - Verify YAML syntax in frontmatter
   - Ensure unique agent name

2. **Tool Access Denied**
   - Review tool restrictions in agent configuration
   - Check if tools are available in environment
   - Verify MCP server connections

3. **Automatic Delegation Not Working**
   - Make description more specific
   - Include trigger keywords in description
   - Check `autoDelegate` setting in configuration

4. **Performance Issues**
   - Limit tool access to necessary ones
   - Reduce concurrent agent limit
   - Optimize system prompts for clarity

## Conclusion

Sub-agents in Claude Code provide a flexible framework for creating specialized AI assistants tailored to your development workflow. By leveraging the subagent system, hooks, and MCP integrations, teams can build powerful automation and assistance tools that significantly improve productivity and code quality. The key is to create focused, well-configured agents that align with your specific needs and workflows.

## References

- [Official Sub-Agents Documentation](https://docs.anthropic.com/en/docs/claude-code/sub-agents)
- [Common Workflows Guide](https://docs.anthropic.com/en/docs/claude-code/common-workflows)
- [MCP Documentation](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [CLI Reference](https://docs.anthropic.com/en/docs/claude-code/cli-reference)
- [Settings Configuration](https://docs.anthropic.com/en/docs/claude-code/settings)