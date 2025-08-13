# Claude Code Octopus

Advanced configuration and extension framework for Claude Code CLI, providing powerful sub-agents, hooks, and custom slash commands for enhanced AI-assisted development workflows.

## 🚀 Features

- **Sub-Agents System** - Create specialized AI assistants with focused expertise
- **Hook System** - Automate workflows and add custom validations
- **Custom Slash Commands** - Build reusable commands for common tasks
- **MCP Integration** - Connect with external tools and services
- **JIRA Integration** - Seamless project management with Atlassian tools

## 📁 Repository Structure

```
claude-code-octopus/
├── .claude/                              # Claude Code configuration
│   ├── agents/                           # Sub-agents definitions
│   │   ├── code-review-agents/          # Code review specialists
│   │   │   ├── bug-detector.md
│   │   │   ├── code-quality-reviewer.md
│   │   │   ├── performance-reviewer.md
│   │   │   ├── security-reviewer.md
│   │   │   └── testing-reviewer.md
│   │   ├── planning-agents/             # Planning specialists
│   │   │   ├── planning-best-practices.md
│   │   │   ├── planning-bug-prevention.md
│   │   │   ├── planning-ci-cd.md
│   │   │   ├── planning-documentation.md
│   │   │   ├── planning-implementation.md
│   │   │   ├── planning-performance-architect.md
│   │   │   ├── planning-quality-advisor.md
│   │   │   ├── planning-security-architect.md
│   │   │   └── planning-testing-strategist.md
│   │   ├── confluence-searcher.md       # Confluence search agent
│   │   ├── lint-type-checker.md         # Linting and type checking
│   │   ├── memory-manager.md            # Memory operations
│   │   └── pdf-analyzer.md              # PDF analysis
│   ├── commands/                         # Custom slash commands
│   │   ├── code-review/                 # Code review commands
│   │   │   ├── agentic-code-review.md
│   │   │   ├── commit-review-by-hash-id.md
│   │   │   └── pr-review.md
│   │   ├── context-memory/              # Memory management
│   │   │   ├── add-memory.md
│   │   │   └── read-memory.md
│   │   ├── planning/                    # Planning commands
│   │   │   ├── agentic-plan-implementation.md
│   │   │   └── jira-task-analyze.md
│   │   ├── research/                    # Research commands
│   │   │   ├── find-solution.md
│   │   │   └── use-context7.md
│   │   ├── testing/                     # Testing commands
│   │   │   ├── test-app-playwright.md
│   │   │   └── use-playwright-mcp.md
│   │   ├── analyze-docs.md
│   │   ├── create-commit.md
│   │   ├── quality-check-python.md
│   │   └── translate-jira-issue-english.md
│   ├── settings.json                    # Project settings
│   └── settings.local.json              # Local settings (gitignored)
├── .github/                              # GitHub configuration
│   └── workflows/                       # GitHub Actions
│       ├── claude-code-review.yml       # Automated code review
│       └── claude.yml                    # Claude integration
├── docs/                                 # Documentation
│   ├── sub-agents-guide.md              # Complete guide for creating and using sub-agents
│   ├── hooks-guide.md                   # Hook system documentation
│   └── custom-slash-commands.md         # Creating custom slash commands
├── .gitignore                            # Git ignore file
├── CLAUDE.md                             # Claude Code configuration
├── LICENSE                               # MIT License
└── README.md                             # This file
```

## 🛠️ Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/claude-code-octopus.git
cd claude-code-octopus
```

2. Create Claude Code directories if they don't exist:
```bash
mkdir -p ~/.claude/agents
mkdir -p ~/.claude/commands
mkdir -p .claude/agents
mkdir -p .claude/commands
```

3. Configure your settings in `.claude/settings.json` or `~/.claude/settings.json`

## 🤖 Sub-Agents

Sub-agents are specialized AI assistants that operate in their own context windows. They help with specific tasks while preserving your main conversation context.

### Creating a Sub-Agent

1. Use the `/agents` command in Claude Code
2. Or manually create a file in `.claude/agents/` or `~/.claude/agents/`:

```markdown
---
name: code-reviewer
description: Expert code review specialist for quality and security
tools: [Read, Grep, Glob]
---

# Code Review Specialist

You are an expert code reviewer focusing on:
- Security vulnerabilities
- Performance issues
- Code maintainability
- Best practices
```

### Available Sub-Agent Types

- **code-reviewer** - Code quality and security analysis
- **debugger** - Error diagnosis and fixing
- **performance-optimizer** - Performance analysis and optimization
- **api-designer** - RESTful and GraphQL API design
- **data-scientist** - Data analysis and SQL optimization
- **security-auditor** - Security vulnerability assessment
- **sre-incident-responder** - Production incident management

## 🪝 Hooks

Hooks allow you to automate workflows by executing scripts in response to Claude Code events.

### Hook Events

- **PreToolUse** - Before tool execution (can block)
- **PostToolUse** - After tool completion
- **UserPromptSubmit** - When user submits prompt
- **SessionStart** - Session initialization
- **Stop** - Main agent completion
- **SubagentStop** - Sub-agent completion

### Example Hook Configuration

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $FILE_PATH",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

## 📝 Custom Slash Commands

Create reusable commands that can be invoked with `/command-name`.

### Creating a Command

1. Create a file in `.claude/commands/`:

```markdown
---
description: Creates a new React component
argument-hint: "component name"
allowed-tools: Write, Read, Bash
model: sonnet
---

# Create Component

Create component: $ARGUMENTS

1. Generate component file
2. Create test file
3. Update exports
```

2. Use in Claude Code:
```
/create-component Button
```

### Dynamic Features

- `$ARGUMENTS` - User input placeholder
- `!`command`` - Execute bash commands
- `@file/path` - Include file contents

## 🔧 Configuration

### Project Configuration (`.claude/CLAUDE.md`)

```markdown
# CLAUDE.md

## MCP Tool Usage
- Always use Context7 for code queries
- Use Atlassian tools for JIRA management

## JIRA Integration
- Transition tasks to "Acceptance" (ID: "6")
- Use English for all operations
```

### Settings (`.claude/settings.json`)

```json
{
  "permissions": {
    "allow": ["mcp__atlassian__*", "Bash", "Read", "Write"],
    "deny": ["Bash(rm -rf *)"]
  },
  "hooks": {
    "PreToolUse": [...],
    "PostToolUse": [...]
  },
  "agents": {
    "autoDelegate": true,
    "maxConcurrent": 3
  }
}
```

## 🔌 MCP Integration

Connect Claude Code with external services:

- **Atlassian** (JIRA, Confluence)
- **GitHub** (Issues, PRs, Actions)
- **Context7** (Documentation, code examples)
- **Playwright** (Browser automation)
- **Filesystem** (Advanced file operations)

### MCP Server Configuration

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-atlassian"],
      "env": {
        "ATLASSIAN_TOKEN": "your_token"
      }
    }
  }
}
```

## 🚦 Getting Started

1. **Set up sub-agents** for your workflow:
   ```bash
   /agents
   ```

2. **Configure hooks** for automation in `.claude/settings.json`

3. **Create custom commands** in `.claude/commands/`

4. **Connect MCP servers** for external integrations

5. **Configure CLAUDE.md** with project-specific instructions

## 📚 Documentation

- [Sub-Agents Guide](docs/sub-agents-guide.md) - Complete sub-agents documentation
- [Hooks Guide](docs/hooks-guide.md) - Hook system and automation
- [Custom Commands](docs/custom-slash-commands.md) - Creating slash commands

## 🔒 Security

⚠️ **Important Security Notes:**

- Hooks execute with your user permissions
- Always validate inputs in hook scripts
- Use absolute paths for commands
- Set appropriate timeouts
- Protect sensitive files with hooks
- Never commit `.claude/settings.local.json`

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Model Context Protocol](https://modelcontextprotocol.io)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)

## 👥 Team

Developed by the Cogniflux team for enhanced AI-assisted development workflows.

---

**Note:** This repository contains configuration templates and documentation for Claude Code. Ensure you have Claude Code CLI installed and properly configured before using these extensions.