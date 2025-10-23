# Hooks Guide for Claude Code

> **Official Documentation**: [Claude Code Hooks](https://docs.anthropic.com/en/docs/claude-code/hooks)
> 
> ⚠️ **Security Warning**: Hooks execute arbitrary commands on your system. USE AT YOUR OWN RISK.

## Table of Contents
1. [What are Hooks](#what-are-hooks)
2. [Configuration](#configuration)
3. [Hook Events](#hook-events)
4. [Hook Input/Output](#hook-inputoutput)
5. [Working with MCP Tools](#working-with-mcp-tools)
6. [Practical Examples](#practical-examples)
7. [Security and Best Practices](#security-and-best-practices)
8. [Debugging and Troubleshooting](#debugging-and-troubleshooting)

## What are Hooks

Hooks are configurable scripts that execute at specific points during Claude Code's workflow. According to the official documentation, hooks are "an open-source standard" that allows developers to:
- Validate and block dangerous operations before they execute
- Add automated checks and formatting after file modifications
- Inject additional context into conversations
- Create custom workflows and integrations
- Monitor and log Claude's activities

### Key Characteristics
- Execute shell commands automatically based on events
- Can block or modify Claude's actions
- Receive JSON input via stdin
- Communicate through exit codes and stdout/stderr
- Support both global and project-level configuration

### When to Use Hooks
- **Code Quality**: Auto-format code, run linters after edits
- **Security**: Block access to sensitive files, validate commands
- **Context Enhancement**: Add timestamps, git info to prompts
- **Workflow Automation**: Trigger builds, tests, deployments
- **Monitoring**: Log tool usage, track file modifications

## Configuration

### Settings File Locations
Hooks are configured in JSON settings files (in order of precedence):
1. **Enterprise managed policies**: `/Library/Application Support/ClaudeCode/managed-settings.json` (highest priority)
2. **User settings**: `~/.claude/settings.json` (global across all projects)
3. **Project settings**: `.claude/settings.json` (shared with team via version control)
4. **Local settings**: `.claude/settings.local.json` (personal, not committed to version control)

### Basic Structure
```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolPattern",
        "hooks": [
          {
            "type": "command",
            "command": "your-command-here",
            "timeout": 60
          }
        ]
      }
    ]
  }
}
```

### Configuration Elements
- **EventName**: The event that triggers the hook (e.g., `PreToolUse`, `PostToolUse`)
- **matcher**: Pattern to match tool names (regex supported, case-sensitive)
- **command**: Shell command to execute (can use environment variables like `$CLAUDE_PROJECT_DIR`)
- **timeout**: Optional timeout in seconds (default: 60)
- **type**: Must be "command" for shell execution

### Matcher Patterns
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",           // Exact match
        "hooks": [...]
      },
      {
        "matcher": "Edit|MultiEdit",  // Regex: matches either
        "hooks": [...]
      },
      {
        "matcher": "mcp__.*",         // Wildcard: all MCP tools
        "hooks": [...]
      },
      {
        "matcher": "*",               // Match all tools
        "hooks": [...]
      }
    ]
  }
}
```

### Project-Specific Scripts
Use `$CLAUDE_PROJECT_DIR` to reference project scripts:
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/format.sh"
          }
        ]
      }
    ]
  }
}
```

## Hook Events

### PreToolUse
Runs **before** a tool executes. Can block the tool from running.

**Common use cases:**
- Validate file paths before writing
- Check commands for dangerous operations
- Request additional confirmation
- Add pre-processing steps

**Supported tools:**
- `Bash` - Shell commands
- `Read`, `Write`, `Edit`, `MultiEdit` - File operations
- `Grep`, `Glob` - Search operations
- `Task` - Sub-agent tasks
- `WebFetch`, `WebSearch` - Web operations
- `mcp__*` - MCP tool operations

### PostToolUse
Runs **after** a tool completes successfully.

**Common use cases:**
- Auto-format code after edits
- Run tests after file changes
- Update documentation
- Trigger builds or deployments
- Log operations

### UserPromptSubmit
Runs when user submits a prompt, before Claude processes it.

**Common use cases:**
- Add timestamps or context
- Validate prompts for security
- Block sensitive information
- Inject system information
- Load project-specific context

### Notification
Runs when Claude sends notifications.

**Triggers when:**
- Claude needs permission for a tool
- Input has been idle for 60+ seconds

**Common use cases:**
- Custom notification systems
- Alert integrations
- Activity monitoring

### Stop
Runs when main Claude agent finishes responding.

**Common use cases:**
- Trigger final checks
- Generate summaries
- Clean up temporary files
- Continue with follow-up tasks

### SubagentStop
Runs when a sub-agent (Task tool) finishes.

**Common use cases:**
- Process sub-agent results
- Chain multiple agents
- Aggregate information

### PreCompact
Runs before context compaction.

**Matchers:**
- `manual` - User-initiated via `/compact`
- `auto` - Automatic due to full context

**Common use cases:**
- Save important context
- Log conversation state
- Prepare for compaction

### SessionStart
Runs when starting or resuming a session.

**Matchers:**
- `startup` - New session
- `resume` - Resumed session
- `clear` - After `/clear` command

**Common use cases:**
- Load development context
- Fetch recent changes
- Set up environment
- Display project status

## Hook Input/Output

### Input Format
Hooks receive JSON via stdin:
```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.jsonl",
  "cwd": "/current/working/directory",
  "hook_event_name": "PreToolUse",
  // Event-specific fields...
}
```

### Output Methods

#### Method 1: Exit Codes (Simple)
- **Exit 0**: Success (stdout shown in transcript mode)
- **Exit 2**: Blocking error (stderr sent to Claude)
- **Other codes**: Non-blocking error (stderr shown to user)

#### Method 2: JSON Output (Advanced)
Return structured JSON for fine control:
```json
{
  "continue": true,                    // Whether to continue
  "stopReason": "Reason if stopping",  // If continue=false
  "suppressOutput": false,             // Hide from transcript
  "decision": "allow|deny|ask",        // Tool permission
  "reason": "Explanation"               // Decision reason
}
```

### Event-Specific Fields

#### PreToolUse
```json
{
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/path/to/file",
    "content": "file content"
  }
}
```

#### PostToolUse
```json
{
  "tool_name": "Bash",
  "tool_input": {
    "command": "npm test"
  },
  "tool_response": {
    "output": "Tests passed",
    "exitCode": 0
  }
}
```

#### UserPromptSubmit
```json
{
  "prompt": "User's input text"
}
```

## Working with MCP Tools

### MCP Tool Naming
MCP tools follow the pattern: `mcp__<server>__<tool>`

Examples:
- `mcp__filesystem__read_file`
- `mcp__github__create_issue`
- `mcp__atlassian__editJiraIssue`

### Targeting MCP Tools
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__github__.*",  // All GitHub tools
        "hooks": [
          {
            "type": "command",
            "command": "echo 'GitHub operation' >> operations.log"
          }
        ]
      },
      {
        "matcher": "mcp__.*__write.*",  // All MCP write operations
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/validate-write.py"
          }
        ]
      }
    ]
  }
}
```

## Practical Examples

### Example 1: Auto-Format Code
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $(git diff --name-only --cached)",
            "timeout": 30
          }
        ]
      }
    ]
  }
}
```

### Example 2: Command Validation
```python
#!/usr/bin/env python3
# .claude/hooks/validate-bash.py
import json
import sys
import re

# Dangerous patterns to block
BLOCKED_PATTERNS = [
    (r'\brm\s+-rf\s+/', "Dangerous rm -rf on root detected"),
    (r'\bsudo\s+rm', "Sudo rm commands are blocked"),
    (r'>\s*/dev/sd[a-z]', "Direct disk write blocked"),
]

try:
    data = json.load(sys.stdin)
    if data.get("tool_name") != "Bash":
        sys.exit(0)
    
    command = data.get("tool_input", {}).get("command", "")
    
    for pattern, message in BLOCKED_PATTERNS:
        if re.search(pattern, command):
            print(f"Security: {message}", file=sys.stderr)
            sys.exit(2)  # Block the command
            
except Exception as e:
    print(f"Hook error: {e}", file=sys.stderr)
    sys.exit(1)

sys.exit(0)
```

### Example 3: Add Context to Prompts
```python
#!/usr/bin/env python3
# .claude/hooks/add-context.py
import json
import sys
import subprocess
from datetime import datetime

try:
    data = json.load(sys.stdin)
    
    # Get git branch
    branch = subprocess.run(
        ["git", "branch", "--show-current"],
        capture_output=True,
        text=True
    ).stdout.strip()
    
    # Get recent commits
    commits = subprocess.run(
        ["git", "log", "--oneline", "-5"],
        capture_output=True,
        text=True
    ).stdout.strip()
    
    context = f"""
Current time: {datetime.now().isoformat()}
Git branch: {branch}
Recent commits:
{commits}
"""
    
    # Output as additional context
    output = {
        "hookSpecificOutput": {
            "hookEventName": "UserPromptSubmit",
            "additionalContext": context
        }
    }
    
    print(json.dumps(output))
    
except Exception as e:
    print(f"Error: {e}", file=sys.stderr)
    sys.exit(1)
```

### Example 4: File Protection
```bash
#!/bin/bash
# .claude/hooks/protect-files.sh

# Read JSON input
INPUT=$(cat)
TOOL_NAME=$(echo "$INPUT" | jq -r '.tool_name')
FILE_PATH=$(echo "$INPUT" | jq -r '.tool_input.file_path // ""')

# Protected files
PROTECTED_FILES=(
    ".env"
    ".env.local"
    "secrets.json"
    "*.key"
    "*.pem"
)

if [[ "$TOOL_NAME" == "Write" || "$TOOL_NAME" == "Edit" ]]; then
    for pattern in "${PROTECTED_FILES[@]}"; do
        if [[ "$FILE_PATH" == *"$pattern"* ]]; then
            echo "Protected file: $FILE_PATH cannot be modified" >&2
            exit 2  # Block the operation
        fi
    done
fi

exit 0
```

### Example 5: Test Runner
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "if [[ $TOOL_INPUT_FILE_PATH == *.test.js ]]; then npm test $(basename $TOOL_INPUT_FILE_PATH); fi",
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

### Example 6: Session Initialization
```python
#!/usr/bin/env python3
# .claude/hooks/session-start.py
import json
import sys
import subprocess

try:
    # Load current JIRA issues
    issues = subprocess.run(
        ["curl", "-s", "https://api.jira.com/current-sprint"],
        capture_output=True,
        text=True
    ).stdout
    
    context = f"""
Project Context Loaded:
- Current sprint issues loaded
- Development environment ready
- Run 'npm test' to verify setup
{issues}
"""
    
    output = {
        "hookSpecificOutput": {
            "hookEventName": "SessionStart",
            "additionalContext": context
        }
    }
    
    print(json.dumps(output))
    
except Exception as e:
    print(f"Session init error: {e}", file=sys.stderr)
    sys.exit(0)  # Don't block session start
```

## Security and Best Practices

### Security Warning
⚠️ **CRITICAL**: As stated in the official documentation, hooks "USE AT YOUR OWN RISK". They can:
- Modify or delete any files your user can access
- Execute system commands with your user permissions
- Access network resources
- Potentially cause data loss or system compromise
- Be exploited through prompt injection if using untrusted sources

### Security Best Practices

#### 1. Input Validation
Always validate and sanitize hook inputs:
```python
# Good: Validate inputs
import shlex
safe_path = shlex.quote(file_path)

# Bad: Direct string interpolation
os.system(f"rm {file_path}")  # Vulnerable!
```

#### 2. Use Absolute Paths
```json
{
  "command": "/usr/local/bin/formatter",  // Good
  "command": "formatter"                  // Bad - could be hijacked
}
```

#### 3. Protect Sensitive Files
```python
BLOCKED_PATHS = ['.env', '.git/', 'node_modules/', '*.key']
if any(blocked in file_path for blocked in BLOCKED_PATHS):
    sys.exit(2)  # Block access
```

#### 4. Set Timeouts
```json
{
  "timeout": 30  // Prevent infinite loops
}
```

#### 5. Log Operations
```bash
echo "$(date): $TOOL_NAME on $FILE_PATH" >> ~/.claude/audit.log
```

### Configuration Safety
- Settings are captured at session start and cached
- External modifications require using `/hooks` command to review changes
- Changes to settings files don't affect running sessions
- Use `claude --debug` to verify hook execution

### Testing Hooks
1. Test commands manually first
2. Use `--debug` flag for detailed output
3. Start with non-blocking hooks (exit code 0)
4. Test edge cases and error conditions
5. Verify with safe test files

## Debugging and Troubleshooting

### Enable Debug Mode
```bash
claude --debug
```

Shows detailed hook execution logs including:
```
[DEBUG] Executing hooks for PostToolUse:Write
[DEBUG] Found 1 hook matchers in settings
[DEBUG] Executing hook command: prettier --write file.js
[DEBUG] Hook completed with status 0
```

### Common Issues

#### Hook Not Triggering
1. Check JSON syntax in settings
2. Verify matcher pattern (case-sensitive)
3. Confirm hook event name spelling
4. Review file permissions

#### Hook Failing
1. Test command manually
2. Check script is executable (`chmod +x`)
3. Verify paths are absolute
4. Review timeout settings

#### Performance Issues
1. Add timeouts to prevent hanging
2. Run heavy operations asynchronously
3. Use `suppressOutput` for verbose commands
4. Batch operations when possible

### Hook Testing Script
```bash
#!/bin/bash
# test-hook.sh - Test a hook with sample input

HOOK_SCRIPT="$1"
TEST_INPUT='{
  "session_id": "test",
  "hook_event_name": "PreToolUse",
  "tool_name": "Write",
  "tool_input": {
    "file_path": "/tmp/test.txt",
    "content": "test"
  }
}'

echo "$TEST_INPUT" | "$HOOK_SCRIPT"
echo "Exit code: $?"
```

### Viewing Hook Configuration
```bash
# Check active hooks in Claude Code
/hooks

# View settings file with jq
cat ~/.claude/settings.json | jq '.hooks'

# List hook scripts
ls -la ~/.claude/hooks/
ls -la .claude/hooks/

# Use claude config to manage settings
claude config get hooks
claude config set hooks.PreToolUse[0].matcher "Bash"
```

### Hook Execution Order
1. Hooks are captured at session start
2. Multiple matching hooks run in parallel
3. All hooks must complete before proceeding
4. Exit code 2 blocks immediately

## Advanced Patterns

### Chaining Hooks
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "prettier --write $FILE && eslint --fix $FILE"
          }
        ]
      }
    ]
  }
}
```

### Conditional Execution
```bash
#!/bin/bash
if [[ "$HOOK_EVENT_NAME" == "PreToolUse" && "$TOOL_NAME" == "Bash" ]]; then
    # Only for Bash PreToolUse
    validate_command "$TOOL_INPUT_COMMAND"
fi
```

### Cross-Hook Communication
```python
# Write state to temp file
with open('/tmp/claude-hook-state.json', 'w') as f:
    json.dump(state, f)

# Read in another hook
with open('/tmp/claude-hook-state.json', 'r') as f:
    state = json.load(f)
```

### Integration with CI/CD
```yaml
# .github/workflows/claude-hook.yml
on:
  workflow_dispatch:
    inputs:
      hook_data:
        description: 'Hook data from Claude'
        required: true

jobs:
  process:
    runs-on: ubuntu-latest
    steps:
      - run: echo "${{ github.event.inputs.hook_data }}"
```

## Hook Library

### Format on Save
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/format-on-save.sh"
          }
        ]
      }
    ]
  }
}
```

### Security Scanner
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/security-scan.py",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

### Git Auto-Commit
```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "git add -A && git commit -m 'Auto-commit: Claude session complete'"
          }
        ]
      }
    ]
  }
}
```

## Conclusion

Hooks provide powerful automation and control over Claude Code's behavior. By understanding hook events, input/output formats, and best practices, you can create sophisticated workflows that enhance productivity while maintaining security and code quality. Start with simple hooks and gradually build more complex automation as you become familiar with the system.

Remember: **Always test hooks carefully and never trust untrusted hook configurations**, as they have full access to your system with your user permissions.

## References

- [Official Claude Code Hooks Documentation](https://docs.anthropic.com/en/docs/claude-code/hooks)
- [Settings Configuration Guide](https://docs.anthropic.com/en/docs/claude-code/settings)
- [CLI Reference](https://docs.anthropic.com/en/docs/claude-code/cli-reference)
- [Security Best Practices](https://docs.anthropic.com/en/docs/claude-code/security)