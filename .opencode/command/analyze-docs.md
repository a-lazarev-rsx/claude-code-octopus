---
description: Orchestrates document analysis using specialized agents for PDFs and
  Confluence with memory persistence
permission:
  bash:
    '*': ask
    git *: allow
    mkdir *: allow
---

# Document Analysis Orchestrator

Analyzes multiple documents (PDFs and Confluence pages) in parallel using specialized agents while managing memory to prevent context overflow.

## Usage Examples:

### Simple format (PDFs only):
```
/analyze-docs report1.pdf report2.pdf report3.pdf
/analyze-docs /path/to/pdf/directory
/analyze-docs ./reports
```

### With flags:
```
/analyze-docs --pdfs "file1.pdf,file2.pdf" --confluence "search query 1,search query 2" --project "project-name" --output "summary.md"
/analyze-docs --pdfs "/path/to/pdfs" --confluence "load testing,performance optimization" --project "finabank"
/analyze-docs --pdfs "./reports" --confluence "как настроить нагрузочное тестирование,масштабирование системы" --project "client-rfp"
```

### Mixed format:
```
/analyze-docs report1.pdf ./reports --confluence "best practices" --project "analysis"
/analyze-docs /docs/pdfs --output "final_report.md"
```

## Instructions:

1. Parse command arguments flexibly:
   ```python
   # Parse $ARGUMENTS
   args = $ARGUMENTS
   
   # Initialize defaults
   pdf_files = []
   confluence_queries = []
   project = "analysis"
   output_file = "analysis_summary.md"
   
   # Helper function to collect PDFs from path
   def collect_pdfs(path):
       pdfs = []
       # Check if it's a directory
       if !`test -d "{path}" && echo "dir" || echo "file"` == "dir":
           # Get all PDFs in directory
           pdf_list = !`find "{path}" -name "*.pdf" -type f`
           pdfs.extend(pdf_list.strip().split('\n'))
       # Check if it's a PDF file
       elif path.endswith('.pdf') and !`test -f "{path}" && echo "exists"` == "exists":
           pdfs.append(path)
       return pdfs
   
   # Parse arguments based on format
   if "--" not in args:
       # Simple format: /analyze-docs path1 path2 path3
       # Each path can be a PDF file or directory
       paths = args.split()
       for path in paths:
           pdf_files.extend(collect_pdfs(path))
   else:
       # Parse with flags
       if "--pdfs" in args:
           pdfs_match = re.search(r'--pdfs\s+["\']?([^"\']+)["\']?', args)
           if pdfs_match:
               paths = [p.strip() for p in pdfs_match.group(1).split(",")]
               for path in paths:
                   pdf_files.extend(collect_pdfs(path))
       
       if "--confluence" in args:
           conf_match = re.search(r'--confluence\s+["\']?([^"\']+)["\']?', args)
           if conf_match:
               confluence_queries = [q.strip() for q in conf_match.group(1).split(",")]
       
       if "--project" in args:
           proj_match = re.search(r'--project\s+["\']?([^"\']+)["\']?', args)
           if proj_match:
               project = proj_match.group(1).strip()
       
       if "--output" in args:
           out_match = re.search(r'--output\s+["\']?([^"\']+)["\']?', args)
           if out_match:
               output_file = out_match.group(1).strip()
       
       # Check for positional arguments (paths without --pdfs flag)
       remaining_args = re.sub(r'--(pdfs|confluence|project|output)\s+["\']?[^"\']+["\']?', '', args).strip()
       if remaining_args:
           paths = remaining_args.split()
           for path in paths:
               pdf_files.extend(collect_pdfs(path))
   
   # Remove duplicates and non-existent files
   pdf_files = list(set([f for f in pdf_files if f]))
   
   # Validate inputs
   if not pdf_files and not confluence_queries:
       return "Error: No PDFs found or search queries provided. Usage: /analyze-docs [files/directories] [--confluence queries] [--project name] [--output file]"
   
   # Show what will be analyzed
   print(f"Found {len(pdf_files)} PDF files to analyze")
   if confluence_queries:
       print(f"Will search Confluence for: {', '.join(confluence_queries)}")
   ```

2. Initialize memory namespace:
   ```
   namespace = "analysis_{project}_{timestamp}"
   ```

3. Launch PDF analysis agents in parallel:
   ```python
   for pdf_file in pdf_files:
     Task(
       description=f"Analyze PDF: {pdf_file}",
       @pdf-analyzer,
       prompt=f"""
       Analyze the PDF file: {pdf_file}
       
       Memory namespace: {namespace}
       Memory key: pdf_{pdf_filename}
       
       Instructions:
       1. Read and analyze the PDF file
       2. Extract key metrics, findings, and insights
       3. Create structured summary with sections:
          - Executive Summary
          - Key Metrics
          - Technical Details
          - Recommendations
       4. Save the analysis to memory using the provided namespace and key
       5. Return a brief summary (max 500 words) of the most important findings
       
       Focus on: performance metrics, scalability data, system requirements, and technical specifications.
       """
     )
   ```

4. Launch Confluence search agents in parallel:
   ```python
   for query in confluence_queries:
     Task(
       description=f"Search Confluence: {query}",
       @confluence-searcher,
       prompt=f"""
       Research topic in Creatio Confluence: {query}
       
       Memory namespace: {namespace}
       Memory key: confluence_{query_id}
       
       Instructions:
       1. Understand the search intent - what problem or information the user needs
       2. Generate multiple search strategies:
          - Direct topic search: "{query}"
          - Related terms: expand query with synonyms and related concepts
          - Best practices search: "{query} best practices" OR "{query} guidelines"
          - Implementation search: "how to {query}" OR "{query} implementation"
          - Troubleshooting: "{query} issues" OR "{query} problems" OR "{query} solutions"
       
       3. Search comprehensively in https://creatio.atlassian.net/wiki:
          - Start with broad searches to understand the topic landscape
          - Follow relevant links and related pages
          - Check child and parent pages for context
          - Look for official documentation, guides, and examples
       
       4. Intelligently filter and prioritize results:
          - Official Creatio documentation
          - Recent updates (check last modified dates)
          - Pages with high relevance to the PDF analysis context
          - Implementation examples and code snippets
       
       5. Extract and synthesize information:
          - Key concepts and definitions
          - Step-by-step procedures
          - Configuration requirements
          - Common pitfalls and solutions
          - Performance recommendations
       
       6. Save structured findings to memory using the provided namespace and key
       
       7. Return a focused summary (max 500 words) that directly addresses the search topic
       
       Remember: The user may not know exact page names or technical terms. Be proactive in exploring related content that could be helpful for their analysis needs.
       """
     )
   ```

5. Wait for all agents to complete and collect their summaries

6. Launch memory compilation agent:
   ```python
   Task(
     description="Compile final analysis",
     @memory-manager,
     prompt=f"""
     Compile comprehensive analysis from memory namespace: {namespace}
     
     Instructions:
     1. Read all analysis results from memory namespace
     2. Identify common themes and patterns across documents
     3. Cross-reference PDF findings with Confluence documentation
     4. Create a unified analysis report with sections:
        - Executive Summary
        - Consolidated Findings by Theme
        - Technical Recommendations
        - Compliance/Requirements Mapping
        - Action Items
     5. Save the final report to: {output_file}
     
     Ensure the report is well-structured, actionable, and addresses all key findings from the analyzed documents.
     """
   )
   ```

7. Present final results:
   ```
   ## Document Analysis Complete
   
   ### Analyzed:
   - PDFs: [list of PDFs]
   - Confluence searches: [list of queries]
   
   ### Results saved to: {output_file}
   
   ### Memory namespace: {namespace}
   (Can be used to resume or expand analysis later)
   ```

## Error Handling:
- If a PDF file doesn't exist, skip it and note in the summary
- If Confluence search fails, retry once then note the failure
- If memory operations fail, fallback to direct file writing

## Example with arguments:
```
$ARGUMENTS = "--pdfs 'load_test_report1.pdf,load_test_report2.pdf' --confluence 'load testing best practices,performance optimization' --project 'finabank-rfp'"
```

This will:
1. Analyze 2 PDF files in parallel
2. Search Confluence for 2 topics in parallel  
3. Save all results to memory under namespace "analysis_finabank-rfp_{timestamp}"
4. Compile final analysis into "analysis_summary.md"

## Additional context: $ARGUMENTS