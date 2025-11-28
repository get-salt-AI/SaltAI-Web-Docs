# Web Scraper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches and aggregates content from one or more web pages, with optional recursive crawling up to a limited depth. It can also run one or multiple natural-language queries against the scraped pages and return formatted answers alongside the collected documents.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gather information from specified URLs, optionally follow links to related pages, and produce concise answers to research questions based on the scraped content. Typical workflows include competitive research, technical documentation review, or compiling references where you provide multiple URLs and one or more queries to be answered from the collected data.

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
<tr><td style="word-wrap: break-word;">urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or multiple starting URLs to scrape. Provide each URL on its own line.</td><td style="word-wrap: break-word;">https://example.com https://docs.example.com/guide</td></tr>
<tr><td style="word-wrap: break-word;">max_depth</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum crawl depth for following links from the provided pages. Depth 0 scrapes only the listed URLs; higher values follow links recursively within the limit.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">queries</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional research questions to answer from the scraped pages. Provide each query on its own line. Leave empty if you only want documents.</td><td style="word-wrap: break-word;">What are the key features? Summarize the installation steps.</td></tr>
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
<tr><td style="word-wrap: break-word;">answers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted text block containing each query and its corresponding answer based on the scraped content. If a query fails, the response notes the exception.</td><td style="word-wrap: break-word;">**Query**  What are the key features?  **Answer**  The product offers A, B, and C with integrations for X and Y.  </td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string array of scraped documents with content and metadata. Entries may include notes if a page was skipped due to an error.</td><td style="word-wrap: break-word;">[   {     "name": "https://example.com",     "content": "...scraped text..."   },   {     "name": "Error",     "content": "Page was skipped due to exception: ..."   } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Depth limit**: max_depth accepts values from 0 to 3. Depth 0 scrapes only the provided URLs.
- **Timeouts**: Very large pages or deep crawls can lead to timeouts. Keep depth conservative for large sites.
- **Queries are optional**: Although provided as an input, you can leave queries empty to only collect documents.
- **Multiple items**: Separate multiple URLs or queries by line breaks.
- **Error propagation**: If a page or query fails, the outputs include explanatory messages embedded in the corresponding document or answer.
- **Access restrictions**: Pages requiring authentication, CAPTCHA, or blocked by robots may not be scraped successfully.

## Troubleshooting
- **Empty URLs error**: If you see an error about missing URLs, ensure at least one non-empty URL is provided (one per line).
- **Timeouts**: Reduce max_depth, limit the number of starting URLs, or target smaller pages to avoid timeouts.
- **Blocked or private pages**: If documents show errors or missing content, verify the pages are publicly accessible and not behind login or anti-bot protection.
- **Broken links**: At higher depths, linked pages might be unavailable. Lower the depth or remove problematic starting URLs.
- **Unhelpful answers**: Provide more specific queries or add more relevant URLs so the node has enough context to answer well.
- **Malformed output JSON**: If downstream parsing fails, ensure you pass the 'documents' output (a JSON string) to a step expecting JSON, or parse it before use.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/webscraper/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

