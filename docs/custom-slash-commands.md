# Creating Custom Slash Commands in Claude Code

## Overview

Custom slash commands allow you to create reusable commands for Claude Code that can be invoked via `/command-name` in the interface.

## File Structure

Custom commands are stored as Markdown files in two locations:
- **Project commands**: `.claude/commands/` in the project root
- **Global commands**: `~/.claude/commands/` in the home directory

## Command Format

Each command is a `.md` file where:
- **File name** defines the command name (e.g., `translate-jira.md` creates the `/translate-jira` command)
- **YAML frontmatter** (optional) for metadata
- **Markdown content** contains command instructions

## Command File Structure

```markdown
---
allowed-tools: Tool1, Tool2(specific-usage)
description: Brief command description
argument-hint: Argument hint (e.g., "issue-key or URL")
model: opus | sonnet | haiku (optional - AI model selection)
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

In `.claude/settings.local.json` file you can specify tool permissions:
```json
{
  "permissions": {
    "allow": ["mcp__atlassian__editJiraIssue", "Bash(npm run lint)"],
    "deny": []
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
- `/bug` - report bugs (can be disabled via environment variable)
- `/help` - get help with using Claude Code

## Personal vs Project Commands

- **Project commands** (`.claude/commands/`) - visible to all project participants
- **Personal commands** (`~/.claude/commands/`) - visible only to you, displayed with "(user)" label

## Debugging

If a command doesn't work:
1. Check YAML frontmatter syntax
2. Ensure file is in the correct directory
3. Verify permissions for used tools
4. Use `claude mcp list` to check MCP commands
5. Verify file name is correct (no spaces, special characters)