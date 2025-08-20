# Creating Custom Slash Commands in Claude Code

> **Official Documentation**: [Claude Code Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)

## Overview

Custom slash commands allow you to create reusable commands for Claude Code that can be invoked via `/command-name` in the interface. These commands enable automation of common tasks and provide a way to extend Claude Code's functionality with custom workflows.

## File Structure

Custom commands are stored as Markdown files in two locations:
- **Project commands**: `.claude/commands/` in the project root
- **Global commands**: `~/.claude/commands/` in the home directory

## Command Format

Each command is a Markdown (`.md`) file where:
- **File name** defines the command name (e.g., `translate-jira.md` creates the `/translate-jira` command)
- **YAML frontmatter** (optional) for metadata and configuration
- **Markdown content** contains the actual command instructions and logic

## Command File Structure

```markdown
---
allowed-tools: Bash(git add:*), Bash(git status:*), Read, Write
description: Brief command description
argument-hint: [parameter description]
model: claude-3-5-sonnet-20241022 | claude-3-5-haiku-20241022 (optional)
---

# Command Title

Instructions for executing the command.

## Dynamic capabilities:
- Arguments: $ARGUMENTS
- Bash commands: !`command here`
- File references: @path/to/file
```

## Key Features

### 1. Arguments
Use `$ARGUMENTS` as a placeholder for user input:
```markdown
Process this input: $ARGUMENTS
```

### 2. Bash Command Execution
The `!` prefix allows executing bash commands:
```markdown
Current directory: !`pwd`
Git status: !`git status`
```

### 3. File References
Use `@` to include file contents:
```markdown
Check this code: @src/main.py
```

### 4. Namespaces
Create subdirectories to organize commands:
- `.claude/commands/frontend/component.md` → `/frontend:component`

### 5. AI Model Selection
You can specify a model in frontmatter for command execution:
- `model: opus` - for complex tasks
- `model: sonnet` - balanced model (default)
- `model: haiku` - for quick simple tasks
- If not specified, uses the current conversation model

### 6. Argument Hints
Use `argument-hint` in frontmatter to provide contextual hints:
```yaml
argument-hint: "issue number or task URL"
```

## Project Example

The current project has a `/translate-jira` command (file: `.claude/commands/translate-jira.md`) that:
- Accepts JIRA issue key or URL via `$ARGUMENTS`
- Uses MCP Atlassian tools to fetch and update tasks
- Translates non-English content to English
- Preserves formatting and technical elements

## Creating Your Own Command

### Step 1: Create directory (if not exists)
```bash
mkdir -p .claude/commands
```

### Step 2: Create command file
```bash
touch .claude/commands/my-command.md
```

### Step 3: Add command content
```markdown
---
description: Description of my custom command
argument-hint: "command parameters"
allowed-tools: Bash, Read, Write, mcp__atlassian
model: sonnet
---

# My Custom Command

Execute the following: $ARGUMENTS

Current context: !`pwd`

## Steps:
1. Validate input data
2. Perform necessary actions
3. Return results
```

### Step 4: Use the command
In Claude Code, type:
```
/my-command [arguments]
```

## Settings and Permissions

Configure tool permissions in settings files (hierarchy from most to least specific):
1. **Enterprise managed policies**: `/Library/Application Support/ClaudeCode/managed-settings.json`
2. **User settings**: `~/.claude/settings.json`
3. **Project settings**: `.claude/settings.json` (shared)
4. **Local settings**: `.claude/settings.local.json` (personal, not in version control)

```json
{
  "permissions": {
    "allow": ["mcp__atlassian__editJiraIssue", "Bash(npm run lint)"],
    "deny": [],
    "ask": ["Write", "Edit"]
  }
}
```

### Hooks
You can configure commands to run before or after tool usage:
```json
{
  "hooks": {
    "PreToolUse": {
      "Bash": "echo 'Running command...'"
    },
    "PostToolUse": {
      "Write": "echo 'File was modified'"
    }
  }
}
```

## MCP Integration

MCP servers can provide slash commands dynamically:
- Format: `/mcp__<server-name>__<prompt-name>`
- Automatically discovered when MCP servers are connected

Examples:
- `/mcp__github__list_prs`
- `/mcp__github__pr_review 456`
- `/mcp__jira__create_issue "Bug in login flow" high`

## Best Practices

1. **Use clear names**: File name should reflect the command's function
2. **Add descriptions**: Always include `description` in frontmatter
3. **Document parameters**: Clearly describe what arguments the command expects
4. **Limit tools**: Only specify necessary tools in `allowed-tools`
5. **Test commands**: Verify command functionality before production use

## Command Examples

### Component Creation Command
```markdown
---
description: Creates a new React component with tests
argument-hint: "component name"
allowed-tools: Write, Read, Bash
model: sonnet
---

# Create React Component

Create component named: $ARGUMENTS

1. Create component file: !`mkdir -p src/components/$ARGUMENTS`
2. Create main file: @templates/component.tsx
3. Create test file: @templates/component.test.tsx
4. Update export index
```

### Performance Analysis Command
```markdown
---
description: Analyzes application performance
argument-hint: "URL or application path"
allowed-tools: Bash, Read, WebSearch
model: opus
---

# Performance Analysis

Analyze: $ARGUMENTS

Current metrics: !`npm run lighthouse`
Compare with industry benchmarks
Suggest optimizations
```

## Built-in Commands

Claude Code provides several built-in commands:
- `/config` - manage configuration settings
- `/bug` - report bugs to GitHub issues
- `/help` - get help with using Claude Code
- `/agents` - list and create custom agents
- `/mcp` - configure MCP servers
- `/hooks` - review hook configuration
- `/compact` - manually trigger context compaction
- `/clear` - clear conversation history

## Personal vs Project Commands

- **Project commands** (`.claude/commands/`) - shared with team via version control
- **Personal commands** (`~/.claude/commands/`) - private to your user account
- When both exist with the same name, conflicts are not currently supported
- Personal commands are marked in the UI for clarity

## Debugging

If a command doesn't work:
1. Check YAML frontmatter syntax (must be valid YAML)
2. Ensure file is in the correct directory (`.claude/commands/` or `~/.claude/commands/`)
3. Verify permissions for used tools in settings files
4. Use `claude mcp list` to check available MCP commands
5. Verify file name is correct (no spaces, special characters)
6. Run `claude --debug` for detailed execution logs
7. Check that dynamic content (`!`, `@`, `$ARGUMENTS`) is properly formatted

## Security Considerations

⚠️ **Important**: Custom commands execute with your user permissions
- Be cautious with commands from untrusted sources
- Review command content before execution
- Use `allowed-tools` to restrict tool access
- Consider using `.claude/settings.local.json` for sensitive configurations

## References

- [Official Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Slash Commands Guide](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
- [MCP Documentation](https://docs.anthropic.com/en/docs/claude-code/mcp)
- [Settings Reference](https://docs.anthropic.com/en/docs/claude-code/settings)