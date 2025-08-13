# Claude Code Octopus

A research and development repository for creating custom commands, agents, and hooks for Claude Code CLI.

## Purpose

This repository serves as an experimental workspace for:
- Developing specialized sub-agents with focused expertise
- Creating custom slash commands for common development tasks  
- Implementing hook systems for workflow automation
- Testing and refining AI-assisted development patterns

## Repository Structure

```
claude-code-octopus/
├── .claude/                              # Claude Code configuration
│   ├── agents/                           # Sub-agents definitions
│   │   ├── code-review-agents/          # Code review specialists
│   │   ├── planning-agents/             # Planning specialists  
│   │   └── ...                          # Other agent types
│   ├── commands/                         # Custom slash commands
│   │   ├── code-review/                 # Code review commands
│   │   ├── context-memory/              # Memory management
│   │   ├── planning/                    # Planning commands
│   │   ├── research/                    # Research commands
│   │   └── testing/                     # Testing commands
│   └── settings.json                    # Project settings
├── docs/                                 # Documentation
│   ├── sub-agents-guide.md              # Sub-agents documentation
│   ├── hooks-guide.md                   # Hook system documentation
│   └── custom-slash-commands.md         # Slash commands guide
└── README.md                             # This file
```

## License

MIT License - see [LICENSE](LICENSE) file for details.