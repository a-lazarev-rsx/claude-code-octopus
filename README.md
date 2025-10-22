# Code Agent Octopus 🐙

A centralized toolkit for AI coding assistants, providing reusable agents, commands, and workflows that work across multiple CLIs.

## Purpose
- Provide production-ready agents, commands, and MCP-aware workflows for AI coding assistants
- Support **Claude Code**, **Codex CLI**, **Factory CLI**, and **GitHub Copilot CLI**
- Ship factory templates (`.factory/`) alongside active configurations so teams can bootstrap new projects quickly
- Document best practices for managing sub-agents, hooks, and automation around Context7-driven development

## Supported CLIs

| CLI | Configuration | Location |
|-----|--------------|----------|
| **Claude Code** | Sub-agents + slash commands | `.claude/` |
| **Codex CLI** | AGENTS.md + commands | `.codex/` |
| **Factory CLI** | AGENTS.md + droids | `.factory/` |
| **Copilot CLI** | AGENTS.md + prompts | `.github/` |

## Repository Map

```
code-agent-octopus/
├── AGENTS.md                  # Vendor-neutral config (Codex, Factory, Copilot)
├── CLAUDE.md                  # Claude Code specific guidance
├── .claude/
│   ├── agents/                # Claude Code sub-agents
│   └── commands/              # Slash commands (Context7-enabled)
├── .codex/
│   └── commands/              # Codex CLI command mirrors
├── .factory/
│   ├── droids/                # Canonical agent templates (source of truth)
│   └── commands/              # Canonical command templates
├── .github/
│   └── prompts/               # GitHub Actions/Copilot prompts
└── docs/
    └── claude-code/           # Guides for commands, agents, hooks
```

## Key Components

- **Planning Agents** (`.factory/droids/planning-agents/*.md`, `.claude/agents/planning-agents/*.md`)  
  Architecture, testing, deployment, and quality advisors designed to delegate long-form reasoning tasks while staying within Context7 guardrails.
- **Code Review Agents** (`.factory/droids/code-review-agents/*.md`)  
  Security, performance, testing, and bug-finding specialists that rely on Context7 lookups for framework-specific guidance.
- **Research & Memory Commands** (`.factory/commands/research/*.md`, `.factory/commands/context-memory/*.md`)  
  Provide repeatable flows for consulting Context7, capturing findings, and replaying project memory.
- **Testing & Tooling Hooks** (`.factory/commands/testing/*.md`, `.claude/commands/testing/*.md`)  
  Automate Playwright, Chrome DevTools MCP, and quality checks across both CLIs.

## Working With Templates

`.factory/` contains canonical templates that sync to CLI-specific directories:

```bash
# Sync to Claude Code
cp -r .factory/droids/* .claude/agents/
cp -r .factory/commands/* .claude/commands/

# Sync to Codex CLI
cp -r .factory/commands/* .codex/commands/
```

**Workflow:**
1. **Edit in Factory** – Make changes to canonical templates in `.factory/`
2. **Sync to CLIs** – Copy to CLI-specific directories (`.claude/`, `.codex/`, etc.)
3. **Validate** – Test in your target CLI
4. **Contribute Back** – Update `.factory/` when changes prove useful

## Quick Start

### Claude Code
```bash
claude
/agents      # List sub-agents
/planning:agentic-jira-task-analyze PROJ-123
```

### Codex/Factory CLI
```bash
codex        # or: factory droid code
# AGENTS.md auto-loads project context
```

### Copilot CLI
```bash
gh copilot   # Reads AGENTS.md and .github/copilot-instructions.md
```

## Documentation

### Project Docs
- **[CLAUDE.md](CLAUDE.md)** – Comprehensive guide for Claude Code (architecture, agents, workflows)
- **[AGENTS.md](AGENTS.md)** – Vendor-neutral config for Codex, Factory, and Copilot CLIs
- **[Custom Slash Commands](docs/claude-code/custom-slash-commands.md)** – Patterns for reusable workflows
- **[Sub-Agents Guide](docs/claude-code/sub-agents-guide.md)** – Designing specialized assistants
- **[Hooks Guide](docs/claude-code/hooks-guide.md)** – Event-driven automation

### Official References
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) · [Codex CLI](https://developers.openai.com/codex/cli) · [Factory CLI](https://docs.factory.ai/factory-cli) · [Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli)
- [Model Context Protocol](https://docs.anthropic.com/en/docs/claude-code/mcp) · [Context7](https://context7.com)

## Security Notes

- Agents and hooks execute with the same permissions as your CLI session; review YAML frontmatter and tool scopes before enabling them.  
- Keep sensitive credentials out of version control—store them in `.claude/settings.local.json` or environment-specific vaults.  
- Follow principle of least privilege when enabling MCP servers (Playwright, GitHub, etc.).
