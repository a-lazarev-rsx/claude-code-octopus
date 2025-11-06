# OpenCode Configuration

This directory contains OpenCode CLI agents and commands, migrated from Claude Code format for compatibility with the [OpenCode](https://opencode.ai) multi-CLI toolkit.

## Directory Structure

```
.opencode/
├── agent/                    # All agents (flat structure)
│   ├── planning-*.md        # 10 planning agents
│   ├── *-reviewer.md        # 5 code review agents
│   └── *.md                 # 4 utility agents
├── command/                  # All commands (flat structure)
│   ├── agentic-*.md         # Multi-agent orchestration commands
│   ├── *-review.md          # Code review commands
│   └── *.md                 # Testing, research, utility commands
├── migrate_from_claude.py   # Migration script
└── README.md                # This file
```

## Available Agents (19 total)

### Planning Agents (10)
- **planning-implementation** - Architecture design and implementation roadmaps with Context7
- **planning-quality-advisor** - Architecture patterns and code quality analysis
- **planning-security-architect** - Security design, authentication flows
- **planning-testing-strategist** - Test strategy and coverage planning
- **planning-ci-cd** - CI/CD pipeline analysis (Jenkins, GitHub Actions, GitLab)
- **planning-performance-architect** - Performance optimization strategies
- **planning-bug-prevention** - Edge case identification before implementation
- **planning-documentation** - Documentation planning for changes
- **planning-best-practices** - Post-implementation best practices review
- **codebase-analyzer** - Deep codebase structure analysis

### Code Review Agents (5)
- **code-quality-reviewer** - Best practices, maintainability, SOLID principles
- **bug-detector** - Bug detection, edge cases, logic errors
- **performance-reviewer** - Performance bottlenecks, optimization opportunities
- **security-reviewer** - Security vulnerabilities, secure coding practices
- **testing-reviewer** - Test coverage, test quality assessment

### Utility Agents (4)
- **confluence-searcher** - Atlassian Confluence search via MCP
- **pdf-analyzer** - PDF document analysis with memory persistence
- **memory-manager** - MCP memory operations and compilation
- **lint-type-checker** - Background linting and type checking

## Available Commands (19 total)

### Planning Commands
- **/jira-task-analyze** - Single-agent JIRA analysis
- **/agentic-plan-implementation** - Comprehensive planning with Context7 research
- **/agentic-jira-task-analyze** - Multi-agent JIRA analysis with codebase deep dive

### Code Review Commands
- **/pr-review** - Multi-agent PR review (GitHub/Bitbucket)
- **/agentic-code-review** - Sequential specialized reviews for staged changes
- **/quality-code-review** - Best practices focused review
- **/commit-review-by-hash-id** - Review specific commits
- **/non-agentic-precommit-review** - Direct review without sub-agents

### Testing Commands
- **/test-app-playwright** - MCP Playwright integration for testing
- **/use-playwright-mcp** - Playwright best practices research
- **/use-chrome-devtools-mcp** - Chrome DevTools MCP research

### Research Commands
- **/use-context7** - Context7 integration for best practices
- **/find-solution** - Multi-source documentation research

### Utility Commands
- **/add-memory** - Store observations in MCP memory
- **/read-memory** - Retrieve stored knowledge
- **/analyze-docs** - PDF and Confluence document analysis
- **/create-commit** - Create git commits with staged changes
- **/quality-check-python** - Python code quality checks
- **/translate-jira-issue-english** - Translate JIRA issues to English

## Usage with OpenCode CLI

### Invoking Agents

Agents are invoked using `@` mention syntax:

```bash
@planning-implementation Create implementation plan for user authentication feature
```

### Running Commands

Commands are invoked with `/` prefix:

```bash
# Analyze JIRA task
/agentic-jira-task-analyze PROJ-123

# Review pull request
/pr-review 456

# Use Context7 for research
/use-context7 FastAPI authentication
```

### Agent Orchestration

OpenCode commands use sequential agent execution (unlike Claude Code's parallel execution):

```bash
# This command will run agents one after another:
/agentic-jira-task-analyze PROJ-123
# 1. @codebase-analyzer runs first
# 2. @planning-implementation runs after codebase analysis completes
# 3. Optional agents run if needed
```

## Key Differences from Claude Code

### 1. Flat Directory Structure
- **Claude Code**: Nested structure (`.claude/agents/planning-agents/`)
- **OpenCode**: Flat structure (`.opencode/agent/`)

### 2. Command Naming
- **Claude Code**: Namespace-based (`/planning:jira-task-analyze`)
- **OpenCode**: Hyphenated (`/jira-task-analyze`)

### 3. Agent Invocation
- **Claude Code**: `Task(subagent_type="agent-name")`
- **OpenCode**: `@agent-name`

### 4. Execution Model
- **Claude Code**: Parallel agent execution supported
- **OpenCode**: Sequential execution only (performance impact: ~40-50% slower)

### 5. Permission Model
- **Claude Code**: Tool-based (`allowed-tools: Task, Read, Write`)
- **OpenCode**: Permission-based (tri-state: allow/ask/deny with bash granularity)

### 6. Tool Specification
- **Claude Code**: Array format (`tools: [Read, Write, Grep]`)
- **OpenCode**: Boolean map (`tools: {read: true, write: true, grep: true}`)

## Frontmatter Format

### Agent Frontmatter
```yaml
---
description: "Brief explanation of agent purpose"
mode: primary
tools:
  read: true
  write: true
  grep: true
  mcp__context7__*: true
permission:
  bash:
    "*": allow  # For agents that create artifacts
---
```

### Command Frontmatter
```yaml
---
description: "Command description"
permission:
  bash:
    "git *": allow
    "mkdir *": allow
    "*": ask
---
```

## Permission Strategy

### Agents with Write Permission (9 agents)
These agents create analysis artifacts and need write access:
- planning-implementation
- codebase-analyzer
- planning-quality-advisor
- planning-security-architect
- planning-testing-strategist
- planning-ci-cd
- planning-performance-architect
- planning-bug-prevention
- planning-documentation

**Permission**: `bash."*": allow` + `write: true`

### Other Agents (10 agents)
Review and analysis agents use conservative defaults:

**Permission**: `bash."*": ask`

## MCP Integration

All agents and commands support MCP (Model Context Protocol) tools:

- **Atlassian MCP** (`mcp__atlassian__*`): JIRA, Confluence integration
- **Context7 MCP** (`mcp__context7__*`): Documentation and best practices
- **Filesystem MCP** (`mcp__filesystem__*`): Deep codebase analysis
- **Memory MCP** (`mcp__memory__*`): Knowledge persistence
- **Playwright MCP** (`mcp__playwright__*`): Browser automation

## Migration from Claude Code

If you make changes to agents/commands in `.claude/` and want to sync to OpenCode:

```bash
# Run the migration script
python3 .opencode/migrate_from_claude.py
```

This will:
1. Read all agents from `.claude/agents/**/*.md`
2. Read all commands from `.claude/commands/**/*.md`
3. Transform frontmatter and content to OpenCode format
4. Write to `.opencode/agent/` and `.opencode/command/`

## Working with Context7

All agents are Context7-aware for up-to-date documentation:

```bash
@planning-implementation Research FastAPI best practices using Context7 before creating plan
```

The agent will:
1. Resolve library IDs using `mcp__context7__resolve-library-id`
2. Fetch 15000+ tokens of documentation using `mcp__context7__get-library-docs`
3. Apply current patterns and avoid deprecated approaches

## Testing

Before using in production:

1. **Test agent invocation**:
   ```bash
   @code-quality-reviewer Review this file: src/main.py
   ```

2. **Test command execution**:
   ```bash
   /use-context7 TypeScript async patterns
   ```

3. **Test multi-agent orchestration**:
   ```bash
   /agentic-jira-task-analyze TEST-123
   ```

## Troubleshooting

### Agent not found
- Verify file exists in `.opencode/agent/`
- Check filename matches agent name (e.g., `planning-implementation.md`)
- Ensure frontmatter has valid `description` field

### Command not found
- Verify file exists in `.opencode/command/`
- Check filename matches command name
- Ensure frontmatter has valid YAML syntax

### Permission denied errors
- Review `permission.bash` settings in frontmatter
- For write operations, ensure `bash."*": allow` is set
- Check file system permissions on target directories

### Sequential execution too slow
- Consider breaking down large tasks into smaller chunks
- Use caching strategies in commands
- Prioritize critical agents only

## Contributing

When adding new agents or commands:

1. Create in `.claude/` first (for consistency with project structure)
2. Test in Claude Code
3. Run migration script to sync to OpenCode
4. Test in OpenCode CLI
5. Document any OpenCode-specific limitations

## Documentation

- **Claude Code Specific**: See `CLAUDE.md` in project root
- **Multi-CLI General**: See `AGENTS.md` in project root
- **OpenCode Docs**: https://opencode.ai/docs/

## License

Same as parent project (Code Agent Octopus).
