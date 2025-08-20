# Claude Code Octopus 🐙

A comprehensive repository for creating and documenting custom commands, agents, and hooks for [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code).

## 🎯 Purpose

This repository serves as both:
1. **Documentation Hub**: Up-to-date guides for extending Claude Code functionality
2. **Extension Library**: Ready-to-use agents, commands, and hooks for development workflows
3. **Learning Resource**: Examples and best practices for Claude Code customization

## ⚡ Quick Start

```bash
# Clone the repository
git clone https://github.com/yourusername/claude-code-octopus.git
cd claude-code-octopus

# Start using with Claude Code
claude
> /agents  # List available agents
```

## 📚 Documentation

### Core Guides
- **[Custom Slash Commands](docs/custom-slash-commands.md)** - Create reusable commands
- **[Sub-Agents Guide](docs/sub-agents-guide.md)** - Build specialized AI assistants
- **[Hooks Guide](docs/hooks-guide.md)** - Automate workflows with event-driven scripts

### Official Resources
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [MCP (Model Context Protocol)](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [CLI Reference](https://docs.anthropic.com/en/docs/claude-code/cli-reference)

## 🗂️ Repository Structure

```
claude-code-octopus/
├── .claude/                     # Claude Code configuration (when in use)
│   ├── agents/                  # Custom sub-agents
│   ├── commands/                # Custom slash commands
│   ├── settings.json           # Project settings
│   └── settings.local.json     # Local settings (gitignored)
├── docs/                        # Documentation
│   ├── custom-slash-commands.md # Commands documentation
│   ├── hooks-guide.md          # Hooks documentation
│   └── sub-agents-guide.md     # Agents documentation
├── examples/                    # Example configurations (planned)
├── LICENSE                      # MIT License
└── README.md                    # This file
```

## 🚀 Features

### Sub-Agents
Specialized AI assistants for specific tasks:
- **Code Review Agents**: Quality, security, performance analysis
- **Planning Agents**: Architecture, implementation, testing strategies
- **Debugging Agents**: Error analysis, root cause identification
- **Documentation Agents**: README generation, API docs, inline comments

### Custom Commands
Reusable slash commands for common tasks:
- Code review workflows
- Project planning and estimation
- Test automation
- Documentation generation
- JIRA/GitHub integration

### Hooks System
Event-driven automation:
- Pre/post tool execution hooks
- File modification triggers
- Session initialization
- Security validation
- Auto-formatting and linting

## 🛠️ Installation & Setup

### Prerequisites
- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed
- Git for version control
- Text editor for creating configurations

### Basic Setup
1. Clone or fork this repository
2. Copy example configurations to your project:
   ```bash
   cp -r examples/.claude ~/your-project/
   ```
3. Customize agents, commands, and hooks for your needs
4. Test in Claude Code:
   ```bash
   claude
   > /agents  # List your agents
   ```

## 🔒 Security Considerations

⚠️ **Important**: Extensions execute with your user permissions
- Review all configurations before use
- Never run untrusted hooks or commands
- Use `.claude/settings.local.json` for sensitive data
- Apply principle of least privilege for tool access

See [Security Best Practices](docs/hooks-guide.md#security-and-best-practices) for details.

## 📝 License

MIT License - see [LICENSE](LICENSE) file for details.
