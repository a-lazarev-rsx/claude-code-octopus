# Repository Purpose

**Code Agent Octopus** is a multi-CLI toolkit that provides reusable agents, commands, and workflows for AI coding assistants. This repository serves as a centralized template library supporting:

- **Claude Code** - Anthropic's CLI with sub-agents and slash commands (`.claude/`)
- **Codex CLI** - OpenAI's CLI with AGENTS.md configuration (`.codex/`)
- **Factory CLI** - Factory.ai's CLI with specialized Droids (`.factory/`)
- **GitHub Copilot CLI** - GitHub's CLI that reads multiple instruction formats (`.github/`)

**Key principle**: `.factory/` contains canonical templates that are synced to CLI-specific directories.

## Multi-CLI Architecture

### Configuration Files Strategy

This project maintains **two primary instruction files**:

1. **CLAUDE.md** (this file) - Claude Code-specific guidance with detailed architecture
2. **AGENTS.md** - Vendor-neutral standard (July 2025) used by Codex CLI, Factory CLI, and Copilot CLI

Both files coexist to support all tools. See `AGENTS.md` for build commands and coding conventions shared across all CLIs.

### How Each CLI Reads Instructions

| CLI Tool | Reads | Format | Location |
|----------|-------|--------|----------|
| **Claude Code** | `CLAUDE.md` | Markdown with detailed sections | Root directory (auto-loaded) |
| **Codex CLI** | `AGENTS.md` | Plain Markdown with semantic headings | Root directory (auto-loaded) |
| **Factory CLI** | `AGENTS.md` | Plain Markdown (same as Codex) | Root directory (auto-loaded) |
| **Copilot CLI** | `AGENTS.md`, `CLAUDE.md`, `.github/copilot-instructions.md` | Markdown with natural language | Multiple locations |

### Directory Structure

```
code-agent-octopus/
├── AGENTS.md                   # Vendor-neutral config (Codex, Factory, Copilot)
├── CLAUDE.md                   # This file - Claude Code specific
├── .claude/
│   ├── agents/                 # Claude Code sub-agents (YAML + Markdown)
│   │   ├── planning-agents/    # Architecture, implementation planners
│   │   └── code-review-agents/ # Quality, security, performance reviewers
│   ├── commands/               # Slash commands with YAML frontmatter
│   │   ├── planning/           # /planning:agentic-jira-task-analyze
│   │   ├── code-review/        # /code-review:pr-review
│   │   ├── testing/            # /testing:test-app-playwright
│   │   ├── research/           # /research:use-context7
│   │   └── context-memory/     # /context-memory:add-memory
│   └── settings.local.json     # Local config (gitignored)
├── .codex/
│   └── commands/               # Codex CLI command mirrors (same as .claude/commands)
├── .factory/
│   ├── droids/                 # Canonical agent templates (source of truth)
│   │   ├── planning-agents/    # 10 specialized planning agents
│   │   └── code-review-agents/ # 5 code review agents
│   └── commands/               # Canonical command templates
│       ├── planning/
│       ├── code-review/
│       ├── testing/
│       ├── research/
│       └── context-memory/
├── .github/
│   └── prompts/                # GitHub Actions/Copilot prompts
└── docs/
    └── claude-code/            # Guides for commands, agents, hooks
        ├── custom-slash-commands.md
        ├── sub-agents-guide.md
        └── hooks-guide.md
```

## Claude Code Specific Architecture

This section describes Claude Code-specific features. For general conventions, see `AGENTS.md`.

### Agent System (Claude Code Only)

**Factory Pattern**: Templates in `.factory/droids/` are canonical sources. Sync to `.claude/agents/` for active use in Claude Code.

**Agent File Format**:
```markdown
---
name: agent-identifier
description: When to use this agent (enables auto-delegation)
tools: [Read, Grep, Glob, mcp__context7__*]
---

# Agent System Prompt

Your role and responsibilities here...
```

**Agent Categories**:

1. **Planning Agents** (`.claude/agents/planning-agents/`)
   - `planning-implementation` - Architecture design, implementation roadmaps with Context7
   - `planning-quality-advisor` - Architecture patterns, code quality analysis
   - `planning-security-architect` - Security design, authentication flows
   - `planning-testing-strategist` - Test strategy and coverage planning
   - `planning-ci-cd` - CI/CD pipeline analysis (Jenkins, GitHub Actions, GitLab)
   - `planning-performance-architect` - Performance optimization strategies
   - `planning-bug-prevention` - Edge case identification before implementation
   - `planning-documentation` - Documentation planning for changes
   - `planning-best-practices` - Post-implementation best practices review
   - `codebase-analyzer` - Deep codebase structure analysis using `mcp__filesystem__*`

2. **Code Review Agents** (`.claude/agents/code-review-agents/`)
   - `code-quality-reviewer` - Best practices, maintainability, SOLID principles
   - `bug-detector` - Bug detection, edge cases, logic errors
   - `performance-reviewer` - Performance bottlenecks, optimization opportunities
   - `security-reviewer` - Security vulnerabilities, secure coding practices
   - `testing-reviewer` - Test coverage, test quality assessment

3. **Utility Agents**
   - `confluence-searcher` - Atlassian Confluence search via MCP
   - `pdf-analyzer` - PDF document analysis with memory persistence
   - `memory-manager` - MCP memory operations and compilation
   - `lint-type-checker` - Background linting and type checking

### Command System (Claude Code Slash Commands)

**Namespace Pattern**: Commands in subdirectories are invoked as `/namespace:command-name`.

**Command File Format**:
```markdown
---
description: Brief command description
argument-hint: "parameter description"
allowed-tools: Task, Read, Write, mcp__atlassian__*
model: claude-sonnet-4-5-20250929  # Optional
---

# Command Title

Instructions with dynamic features:
- $ARGUMENTS for user input
- !`bash command` for execution
- @path/to/file for file inclusion
```

**Available Commands**:

- **`/planning:*`** - JIRA analysis, implementation planning
  - `/planning:agentic-jira-task-analyze [JIRA-KEY]` - Multi-agent JIRA analysis with codebase deep dive
  - `/planning:agentic-plan-implementation [requirements]` - Comprehensive planning with Context7 research
  - `/planning:jira-task-analyze [JIRA-KEY]` - Single-agent JIRA analysis

- **`/code-review:*`** - Code quality, PR reviews
  - `/code-review:pr-review [PR_NUMBER] [branch]` - Multi-agent PR review (GitHub/Bitbucket)
  - `/code-review:agentic-code-review` - Parallel specialized reviews for staged changes
  - `/code-review:quality-code-review` - Best practices focused review
  - `/code-review:commit-review-by-hash-id [hash]` - Review specific commits
  - `/code-review:non-agentic-precommit-review` - Direct review without sub-agents

- **`/testing:*`** - Browser automation, testing
  - `/testing:test-app-playwright [url]` - MCP Playwright integration for testing
  - `/testing:use-playwright-mcp [query]` - Playwright best practices research
  - `/testing:use-chrome-devtools-mcp [query]` - Chrome DevTools MCP research

- **`/research:*`** - Documentation lookup
  - `/research:use-context7 [tech]` - Context7 integration for best practices
  - `/research:find-solution [problem]` - Multi-source documentation research

- **`/context-memory:*`** - Knowledge persistence
  - `/context-memory:add-memory` - Store observations in MCP memory
  - `/context-memory:read-memory` - Retrieve stored knowledge

- **`/analyze-docs`** - PDF and Confluence document analysis with memory
- **`/create-commit`** - Create git commits with staged changes
- **`/quality-check-python`** - Python code quality checks

### Key Workflows (Claude Code)

#### Multi-Agent Orchestration Pattern

Commands like `/planning:agentic-jira-task-analyze` demonstrate parallel agent execution:

1. **Phase 0**: Fetch JIRA task, save to `working-docs/analysis/[ISSUE-KEY]/jira-task.md`
2. **Phase 1**: Launch core agents **in parallel** using Task tool
   - `codebase-analyzer` - Deep filesystem analysis with `mcp__filesystem__*`
   - `planning-implementation` - Architecture with Context7 research
3. **Phase 2**: Launch optional agents based on complexity (security, testing)
4. **Phase 3**: Synthesize all outputs into `working-docs/analysis/[ISSUE-KEY]/final-plan.md`

**Important**: All agents save to `working-docs/analysis/[ISSUE-KEY]/` with consistent naming:
- `jira-task.md` - Original requirements
- `codebase-analysis.md` - Structure and integration points
- `implementation-plan.md` - Detailed roadmap
- `security-analysis.md` - Security considerations (optional)
- `testing-strategy.md` - Test planning (optional)

#### Context7 Integration Workflow

All agents and commands are Context7-aware. Standard pattern:

```markdown
1. Identify technology stack from code/requirements
2. Call mcp__context7__resolve-library-id for each framework
3. Call mcp__context7__get-library-docs with 15000+ tokens for detailed docs
4. Research multiple implementation patterns
5. Check for breaking changes and deprecations
6. Incorporate current best practices into plans
```

#### MCP Tool Usage in Claude Code

- **Atlassian MCP** (`mcp__atlassian__*`): JIRA issue management, Confluence search, task transitions
- **Filesystem MCP** (`mcp__filesystem__*`): Deep codebase analysis, directory trees, file search
- **Memory MCP** (`mcp__memory__*`): Knowledge persistence across sessions, entity graphs
- **Playwright MCP** (`mcp__playwright__*`): Browser automation, testing, screenshots
- **IDE MCP** (`mcp__ide__*`): Language diagnostics, code execution in Jupyter

### Working with Claude Code Templates

#### Syncing from Factory to Claude Code

```bash
# Sync agent template
cp .factory/droids/planning-agents/planning-implementation.md .claude/agents/planning-agents/

# Sync command template
cp .factory/commands/research/use-context7.md .claude/commands/research/

# Verify in Claude Code
claude
/agents      # List and verify agent is registered
/commands    # List available slash commands
```

#### Creating New Claude Code Agents

1. Create file: `.claude/agents/[category]/[agent-name].md`
2. Add YAML frontmatter with `name`, `description`, and `tools` array
3. Write detailed system prompt with role and responsibilities
4. Test with `/agents` command in Claude Code
5. If proven useful, copy to `.factory/droids/` as canonical template

#### Creating New Slash Commands

1. Create file: `.claude/commands/[namespace]/[command-name].md`
2. Add YAML frontmatter:
   ```yaml
   ---
   description: Brief command description
   argument-hint: "parameter description"
   allowed-tools: Task, Read, Write, mcp__atlassian__*
   ---
   ```
3. Use dynamic features: `$ARGUMENTS`, `!`command``, `@file/path`
4. Test invocation: `/namespace:command-name [args]`
5. Mirror to `.codex/commands/` if applicable

## Cross-CLI Synchronization Patterns

### Factory → Active Directories

`.factory/` serves as the **single source of truth**. Workflow:

1. **Edit in Factory** - Make changes to `.factory/droids/` or `.factory/commands/`
2. **Sync to Active CLIs**:
   ```bash
   # Sync to Claude Code
   cp -r .factory/droids/* .claude/agents/
   cp -r .factory/commands/* .claude/commands/

   # Sync to Codex CLI
   cp -r .factory/commands/* .codex/commands/
   ```
3. **Validate** - Test in each CLI before committing

### CLI-Specific Adaptations

Different CLIs have different formats:

| Feature | Claude Code | Codex/Factory CLI | Copilot CLI |
|---------|-------------|-------------------|-------------|
| **Agent Format** | YAML frontmatter + Markdown | N/A (uses AGENTS.md) | `.github/prompts/*.md` |
| **Command Format** | YAML frontmatter + Markdown | Plain Markdown | Plain Markdown |
| **Tool Permissions** | `allowed-tools` in frontmatter | CLI flags `--allow-tool` | CLI flags `--allow-tool` |
| **MCP Support** | Native MCP integration | MCP via Agents SDK | MCP servers |

### Maintaining Consistency

When updating templates:

1. **Update `.factory/`** first (canonical source)
2. **Sync to `.claude/`** for Claude Code agents/commands
3. **Sync to `.codex/`** for Codex CLI commands (if format compatible)
4. **Update `AGENTS.md`** for general conventions shared across CLIs
5. **Update this file** (`CLAUDE.md`) for Claude Code-specific guidance

## Development Patterns

### Agent Communication (Claude Code)

Agents use filesystem for inter-agent communication. Pattern:

```markdown
1. Orchestrator command creates: working-docs/analysis/[ISSUE-KEY]/
2. Each agent writes to: working-docs/analysis/[ISSUE-KEY]/[agent-name].md
3. Orchestrator compiles all outputs into final deliverable
```

### Parallel Agent Execution (Claude Code)

Launch independent agents in parallel using Task tool:

```markdown
Use single message with multiple Task tool calls when no dependencies exist.

Example: codebase-analyzer and planning-implementation can run simultaneously.
```

### Context7 Best Practices

Always research before implementation:

1. Identify all technologies in the stack
2. Resolve library IDs for each technology
3. Fetch 15000+ tokens of documentation
4. Apply current patterns, avoid deprecated approaches
5. Document Context7 sources in plans

## Common Operations

### Claude Code Inspection

```bash
claude
/agents      # List registered sub-agents
/commands    # List custom slash commands
/hooks       # Review hook configuration
/mcp         # Check MCP server status
```

### JIRA Task Workflow (Claude Code)

```bash
/planning:agentic-jira-task-analyze PROJ-123
# Creates: working-docs/analysis/PROJ-123/
# Outputs: jira-task.md, codebase-analysis.md, implementation-plan.md, etc.
```

### Code Review Workflow (Claude Code)

```bash
/code-review:pr-review 456
# Multi-agent review: quality, security, performance, testing, bugs
```

### Cross-CLI Command Testing

```bash
# Test in Claude Code
claude
/research:use-context7 FastAPI

# Test equivalent in Codex CLI (if ported)
codex
/commands reload
/research:use-context7 FastAPI

# Test in Factory CLI
factory droid exec "Research FastAPI best practices using Context7"
```

### Syncing Factory to Codex

```bash
# After updating .claude/commands/
rsync -av .claude/commands/ .codex/commands/

# Verify in Codex
codex
/commands reload
```

## Documentation References

### Claude Code Specific
- **Custom Slash Commands**: `docs/claude-code/custom-slash-commands.md`
- **Sub-Agents Guide**: `docs/claude-code/sub-agents-guide.md`
- **Hooks Guide**: `docs/claude-code/hooks-guide.md`
- **Official Claude Code Docs**: https://docs.anthropic.com/en/docs/claude-code
- **MCP Documentation**: https://docs.anthropic.com/en/docs/claude-code/mcp
- **Context7 Docs**: https://context7.com

### Multi-CLI Resources
- **AGENTS.md** (this repo) - Shared conventions for Codex, Factory, Copilot
- **OpenAI Codex Docs**: https://developers.openai.com/codex/cli
- **Factory CLI Docs**: https://docs.factory.ai/factory-cli
- **GitHub Copilot CLI**: https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli

## Security Considerations

### Claude Code Specific
- Agents execute with CLI session permissions
- Review YAML frontmatter and `tools` arrays before enabling agents
- Store credentials in `.claude/settings.local.json` (gitignored)
- Validate tool scopes in `allowed-tools` frontmatter
- Follow principle of least privilege for MCP server access

### Cross-CLI Security
- Factory templates in `.factory/` should use minimal permissions
- When syncing to multiple CLIs, audit tool permissions for each target
- Different CLIs may interpret permissions differently
- Test in sandboxed environment before production use

## Quick Start for Different CLIs

### Using Claude Code (Primary)
```bash
claude
/agents      # See available sub-agents
/planning:agentic-jira-task-analyze PROJ-123
```

### Using Codex CLI
```bash
codex
# AGENTS.md is auto-loaded
# Run commands from .codex/commands/
```

### Using Factory CLI
```bash
factory droid code
# AGENTS.md guides the Code Droid
# Use factory templates from .factory/droids/
```

### Using Copilot CLI
```bash
gh copilot
# Reads AGENTS.md, CLAUDE.md, .github/copilot-instructions.md
# Use for GitHub-integrated workflows
```

## Contributing Back to Factory

When you improve an agent or command:

1. Test in your target CLI (Claude Code, Codex, etc.)
2. Update the canonical source in `.factory/`
3. Sync to other CLI directories if applicable
4. Update both `AGENTS.md` and `CLAUDE.md` as needed
5. Document CLI-specific quirks in this file
6. Create PR with validation notes from multiple CLIs
