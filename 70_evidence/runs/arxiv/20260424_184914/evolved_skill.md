---
name: arxiv
description: Search and retrieve academic papers from arXiv using their free REST API. No API key needed. Search by keyword, author, category, or ID. Combine with web_extract or the ocr-and-documents skill to read full paper content.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [Research, Arxiv, Papers, Academic, Science, API]
    related_skills: [ocr-and-documents]
---

# Enhanced Task Instructions for arXiv and Semantic Scholar API Integration

Your task is to interact with the arXiv and Semantic Scholar APIs to perform various operations related to academic research papers. You should use `curl` and Python to execute API calls and parse responses. Additional dependencies or API keys are not required for these tasks.

## Operations:

1. **Search for Papers by Category**:
   - Use the arXiv API to search for research papers in a specific category, such as "math.OC".
   - You'll execute a curl command to get papers from a specified category.
   - Example curl command:
     ```bash
     curl -s "https://export.arxiv.org/api/query?search_query=cat:math.OC&max_results=10"
     ```
   - Output should be parsed using Python to display:
     - Title of the paper,
     - Authors,
     - Publication date,
     - Categories,
     - Abstract excerpt,
     - PDF Link.

2. **Retrieve Specific Paper by arXiv ID**:
   - Fetch metadata of a paper using its arXiv ID.
   - Example:
     ```bash
     curl -s "https://export.arxiv.org/api/query?id_list=2402.03300"
     ```
   - Parse the XML response using Python to clearly extract and display:
     - Title,
     - Authors,
     - Published Date,
     - Abstract,
     - Categories,
     - PDF Link.

3. **Find Influential Citing Papers**:
   - Utilize the Semantic Scholar API to find influential papers that cite a specific arXiv paper by its ID.
   - Example:
     ```bash
     curl -s "https://api.semanticscholar.org/graph/v1/paper/arXiv:2402.03300/citations?fields=title,authors,year,citationCount&limit=10"
     ```
   - Parse the JSON response using Python to display:
     - Title,
     - Authors,
     - Publication Year,
     - Citation Count for each citing paper.

## Considerations:

- Utilize sorting and filtering as needed to refine results from both APIs.
- Ensure the presentation of data is structured and easy to read.
- While parsing API responses, handle XML and JSON formats efficiently using Python's standard libraries.
- Prioritize clarity and precision in extracting and displaying necessary details like paper titles, authors, publication data, etc.
- Ensure the results are actionable and informative for academic research pursuits.

Following these instructions will ensure efficient utilization of arXiv and Semantic Scholar APIs in academic research tasks, without external libraries or authentication procedures.
