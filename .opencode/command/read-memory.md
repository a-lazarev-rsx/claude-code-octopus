---
description: Read and retrieve information from MCP memory with optional filtering
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
---

# MCP Memory Reading

Read and retrieve information from MCP memory with optional filtering and context.

## Instructions:
1. **Parse additional context** from: $ARGUMENTS
2. **Determine retrieval scope**:
   - Use context to identify what data to retrieve
   - If no specific context, retrieve all stored data
3. **Execute memory read operations**:
   - Query memory based on context/filters
   - Retrieve relevant information
4. **Process and present results**:
   - Format retrieved data for clarity
   - Highlight relevant information based on context
   - Show metadata (timestamps, keys, etc.)
5. **Handle empty results**:
   - Inform if no matching data found
   - Suggest alternative queries if applicable

## Query options:
- Retrieve all memory contents
- Filter by key patterns
- Search by content keywords
- Filter by date ranges (if timestamps available)

## Note:
The additional context helps narrow down the search and retrieve the most relevant information from memory.