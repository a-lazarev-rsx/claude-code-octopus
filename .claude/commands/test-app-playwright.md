---
description: Execute application testing using MCP Playwright
allowed-tools: mcp__playwright, Read, Bash
---

# Application Testing with MCP Playwright

Execute automated application testing with the following instructions: $ARGUMENTS

## Testing Process:

1. Retrieve test user credentials from ./.test-credentials/test-credentials.md
2. Launch browser via MCP Playwright
3. Navigate to the application
4. Login with test credentials
5. Perform testing according to additional instructions
6. Take screenshots of key steps if requested in instructions
7. Check console for errors
8. Analyze network requests if needed
9. Complete testing and close browser

## Important Notes:

- Always check browser console for JavaScript errors
- Document all discovered issues
- Use explicit waits for dynamic content
