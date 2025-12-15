# Web Scraper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches and parses web pages starting from one or more URLs and (optionally) answers user-provided queries based on the scraped content. It sends a request to the Agents service, handles errors, and returns a formatted Q&A summary plus the raw scraped documents as a JSON string. Depth-limited recursive scraping is supported up to a maximum depth of 3.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract content from web pages and optionally get concise answers to specific questions about that content. Provide one or more URLs (newline-separated), choose a recursion depth for following links, and optionally add queries (newline-separated). The node returns a human-readable answers summary and a JSON string containing all scraped documents.

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
<tr><td style="word-wrap: break-word;">urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or multiple starting URLs to scrape. Separate multiple URLs with line breaks. At least one non-empty URL is required.</td><td style="word-wrap: break-word;">https://example.com https://example.org/articles/123</td></tr>
<tr><td style="word-wrap: break-word;">max_depth</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum recursion depth for following links from the provided pages. At depth 0, only the provided URLs are scraped. Valid range: 0–3.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">queries</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional questions to answer based on the scraped content. Separate multiple queries with line breaks. Can be left empty to only retrieve documents.</td><td style="word-wrap: break-word;">What is the main topic of the page? List the key takeaways.</td></tr>
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
<tr><td style="word-wrap: break-word;">answers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted text summary containing each query and its corresponding answer. If a query fails, the response includes a note indicating it was skipped due to an exception.</td><td style="word-wrap: break-word;">**Query**  What is the main topic?  **Answer**  The page discusses example domain usage and purpose.  </td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representing the list of scraped documents. Each document includes fields such as name and content. If a page failed to load, its content explains the exception.</td><td style="word-wrap: break-word;">[   {     "name": "https://example.com",     "content": "Example Domain ..."   },   {     "name": "Error",     "content": "Page was skipped due to exception:\n<error details>"   } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **External service dependency**: This node calls the Agents service endpoint at agents/web_scrape. Network connectivity and correct service configuration are required.
- **Timeout**: Requests time out after 300 seconds. Large pages or high recursion depth can increase processing time.
- **Depth limit**: max_depth is capped at 3. Depth 0 scrapes only the provided URLs.
- **Input formatting**: Provide multiple URLs or queries as separate lines. At least one URL is required; queries can be empty.
- **Error propagation**: Non-200 responses or JSON parsing issues will raise an exception. Failed pages or queries are returned with explanatory messages in the outputs.

## Troubleshooting
- **Non-200 or request failure**: Verify the URLs are valid and reachable, and ensure the Agents service endpoint is configured and accessible. Try reducing the number of URLs.
- **Timeouts**: Reduce max_depth, provide fewer/lighter pages, or try again when the network is stable. Very large or complex pages increase processing time.
- **Empty answers**: If no queries are provided, the answers output will be empty or minimal by design. Add queries to receive Q&A output.
- **Malformed documents JSON**: The documents output is a JSON string. If downstream nodes fail to parse it, ensure they expect a JSON string and not a Python object.
- **Unexpected HTML or proxy pages**: If JSON decoding errors occur, the service may be returning an HTML error page (e.g., proxy/auth). Check service URL configuration and authentication requirements.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/webscraper/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

