---
description: Use MCP memory to store and retrieve information with validation
allowed-tools: mcp__memory, Read, Grep
---

# MCP Memory Management

Use MCP memory to store, retrieve, and manage information with validation.

## Instructions:
1. **Parse additional instructions** from: $ARGUMENTS
2. **Validate input data** before storing:
   - Check data format and structure
   - Verify required fields are present
   - Ensure data consistency
3. **Execute memory operations** based on instructions
4. **Validate results** after operations:
   - Confirm data was stored/retrieved correctly
   - Check for any discrepancies
5. **Handle validation failures**:
   - If data validation fails, ask user whether to:
     - Continue with current data
     - Correct the data and retry
     - Cancel the operation

## Validation checks:
- Data completeness
- Format correctness
- Logical consistency
- No duplicate entries (if applicable)

## Note:
Always confirm with the user before proceeding if validation reveals any issues or discrepancies in the data.