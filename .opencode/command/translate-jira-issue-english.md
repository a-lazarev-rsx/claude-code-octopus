---
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
---
# Translate JIRA Issue to English

Automatically translate JIRA issue content to English and update the issue. Supports translation from Russian, Polish, Ukrainian, Spanish, Romanian, and other languages.

## Input
Issue: $ARGUMENTS

## Instructions

1. Extract the issue key from the provided argument:
   - If it's a URL like `https://yoursite.atlassian.net/browse/PROJ-123`, extract `PROJ-123`
   - If it's already a key like `PROJ-123`, use it directly

2. Fetch the JIRA issue using MCP Atlassian tools:
   - Use `mcp__atlassian__getJiraIssue` to get the issue details
   - Extract cloudId from the URL if provided, or use `mcp__atlassian__getAccessibleAtlassianResources`
   - Get all fields including summary and description

3. Detect language and translate:
   - Automatically detect if content is not in English
   - If already in English, skip translation
   - Translate summary and description to English
   - Preserve all technical terms, code blocks, URLs
   - Keep JIRA formatting (@mentions, [~username], {code} blocks, etc.)
   - Maintain all markdown, lists, and tables

4. Update the JIRA issue:
   - Use `mcp__atlassian__editJiraIssue` to update the issue
   - Update both summary and description fields with English translations
   - Preserve all other fields unchanged

## Error Handling
- If issue not found, report clear error
- If already in English, report "Issue is already in English"
- If update fails, report the error with details

## Required MCP Tools
- mcp__atlassian__getAccessibleAtlassianResources
- mcp__atlassian__getJiraIssue
- mcp__atlassian__editJiraIssue

Note: This command requires MCP Atlassian integration to be configured and authenticated.