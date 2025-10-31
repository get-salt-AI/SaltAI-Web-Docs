# Web Scraper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches and aggregates content from one or more web pages and optionally answers user-provided queries based on the scraped content. Supports limited recursive crawling with a configurable depth. Returns a readable Q&A summary string and a JSON-formatted string of scraped documents.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gather information from specified URLs and, if desired, generate answers to specific questions using the scraped content. Typical workflows include research, summarization, and preparing references: provide URLs, set a crawl depth (0–3), optionally include queries, and consume the returned answers and raw documents.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or multiple starting URLs to scrape. Separate multiple URLs with line breaks. At least one non-empty URL is required.</td><td style="word-wrap: break-word;">https://example.com https://docs.python.org</td></tr>
<tr><td style="word-wrap: break-word;">max_depth</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum recursive scraping depth. 0 scrapes only the provided URLs; higher values follow in-page links up to the given depth (max 3). Larger pages or deeper crawls may increase processing time.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">queries</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional questions to answer using the scraped content. Separate multiple queries with line breaks. Can be left empty to only return documents.</td><td style="word-wrap: break-word;">What are the key features? Provide a short summary of each page.</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">answers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted string containing each query and its corresponding answer derived from the scraped pages. If a query failed, the output includes a note indicating it was skipped due to an exception.</td><td style="word-wrap: break-word;">**Query**  What are the key features?  **Answer**  The site highlights A, B, and C as core features.  </td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string of the scraped documents. Each entry contains the page name and content; error entries include a note if a page was skipped due to an exception.</td><td style="word-wrap: break-word;">[   { "name": "Example Domain", "content": "This domain is for use in illustrative examples..." } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Depth 0 scrapes only the provided URLs; increasing depth follows links and can significantly increase runtime.
- Large or complex pages and higher depths may cause processing to approach the node timeout (300 seconds).
- At least one URL is required; queries can be left empty if you only need raw documents.
- Answers are produced only when queries are provided. If a query fails, the answer text will indicate it was skipped due to an exception.
- Documents output is a JSON string; you may need to parse it in downstream nodes to access individual entries.

## Troubleshooting
- If you see 'Request to Agents service failed', verify network connectivity and that the target service is available, then retry.
- If the node times out, reduce max_depth, decrease the number of URLs, or choose smaller/lighter pages.
- If documents is empty or minimal, ensure the URLs are valid and publicly accessible (no authentication or heavy client-side rendering required).
- If answers are empty, provide one or more queries or check the documents output to confirm scraping succeeded.
- If the documents string indicates pages were skipped due to exceptions, try the URLs individually, reduce depth, or remove problematic links.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/webscraper/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

