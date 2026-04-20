# Web Scraper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Web Scraper sends a list of URLs to the Agents service, which crawls and parses the pages up to a specified depth and can run queries against the collected content. It returns formatted answers for each query and a structured JSON list of all scraped documents, including any that failed. This node is ideal when you need live web information ingested into a workflow and analyzed or summarized through natural-language queries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/agents/webscraper.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Web Scraper when your workflow needs to pull fresh data directly from websites and then analyze, summarize, or compare it. Typical use cases include technical or market research across product pages, synthesizing content from documentation sites, or reading recent blog posts and then asking specific questions (for example, comparing features, extracting pros/cons, or identifying key themes). Place this node early or mid-pipeline: upstream, you might generate or filter URLs and queries using prompt/agent or search nodes; downstream, feed the `answers` into LLM or reporting nodes for further summarization, and the `documents` JSON into JSON processing or storage nodes.

Configure the node by listing each starting URL on its own line in `urls`, choosing a `max_depth` between 0 and 3 to control how many link levels are followed, and optionally listing one or more queries on separate lines in `queries`. The node calls the Agents service `/web_scrape` endpoint, which performs the actual crawling, parsing, and question answering. For best results, start with a small depth (0 or 1), especially on large or unknown sites, ensure the URLs are publicly accessible, and phrase queries precisely so they can be answered from the scraped content. Web Scraper works well together with WebSearcher (to discover URLs first) and JSONExtractor or other JSON-aware nodes (to further process the `documents` output).

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
<tr><td style="word-wrap: break-word;">urls</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or multiple starting URLs to scrape, separated by line breaks. Each non-empty line must be a valid HTTP or HTTPS URL. At least one non-empty URL is required; otherwise, the node raises an error. URLs should be publicly accessible, as pages behind authentication, paywalls, or strict network controls may fail or be returned as error documents.</td><td style="word-wrap: break-word;">https://docs.python.org/3/whatsnew/3.12.html https://pytorch.org/blog/</td></tr>
<tr><td style="word-wrap: break-word;">max_depth</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum recursion depth for following links starting from the given URLs. 0 means only the listed pages are scraped; 1 allows following links found on those pages; higher values increase how far the scraper explores, up to a maximum of 3. Larger depths can significantly increase the number of pages processed and the chance of timeouts on big or complex sites.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">queries</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional list of queries to answer based on the scraped content. Each non-empty line is treated as a separate query. If left empty, the node still scrapes and returns `documents` but produces no semantic `answers`. Queries should be natural-language questions or instructions whose answers can reasonably be derived from the scraped pages.</td><td style="word-wrap: break-word;">Summarize the key performance improvements introduced in Python 3.12. From the latest PyTorch blog posts, what deployment topics are most frequently discussed?</td></tr>
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
<tr><td style="word-wrap: break-word;">answers</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single formatted string that concatenates all query-answer pairs. For each query, it includes a "Query" section with the original query text and an "Answer" section with the response provided by the Agents service, separated by blank lines. If a specific query fails, its response text is prefixed with a notice that the query was skipped due to an exception, but the node as a whole may still succeed.</td><td style="word-wrap: break-word;">**Query**  Summarize the key performance improvements introduced in Python 3.12.  **Answer**  Python 3.12 introduces runtime performance improvements in CPython, a new f-string parser, better error messages, and typing enhancements, resulting in faster execution and a smoother developer experience.  **Query**  From the latest PyTorch blog posts, what deployment topics are most frequently discussed?  **Answer**  Recent PyTorch blog posts emphasize production deployment, including TorchServe, optimized inference, mobile and edge deployment, and best practices for monitoring and scaling models in production.  </td></tr>
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representing the list of scraped documents. Each document usually includes fields like `name`, `url`, and `content`, and may include other metadata provided by the Agents service. If a page fails to load or parse, the `name` is set to "Error" and the `content` is prefixed with a message explaining that the page was skipped due to an exception. This output is intended to be parsed by JSON-aware downstream nodes or stored for later processing.</td><td style="word-wrap: break-word;">[   {     "name": "https://docs.python.org/3/whatsnew/3.12.html",     "url": "https://docs.python.org/3/whatsnew/3.12.html",     "content": "Python 3.12 is a significant release that includes performance improvements, a new f-string parser, more precise error messages, and typing enhancements, among other changes. ..."   },   {     "name": "Error",     "url": "https://example.com/protected-page",     "content": "Page was skipped due to exception:\nHTTP 403: Forbidden"   } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node uses a remote Agents service with a 300-second timeout. Many URLs, high `max_depth`, or large/slow sites can approach this limit and cause timeouts, so start with smaller batches and shallow depth.
- **Limitations**: Only publicly accessible HTTP/HTTPS content is supported. Pages requiring login, captchas, or with aggressive rate limiting may not be scraped successfully and will appear as error documents.
- **Behavior**: `urls` and `queries` are split on newlines and trimmed; empty lines are removed. If no non-empty URLs remain, the node raises a ValueError before sending any request.
- **Behavior**: The node differentiates between request-level failures (which raise an exception and stop execution) and per-page or per-query failures (which are preserved in the `documents` and `answers` outputs with explanatory messages).
- **Integration**: The `documents` output is a JSON string; to work with individual fields, feed it into JSON-capable nodes (such as JSONExtractor) instead of treating it as plain text.

## Troubleshooting
- **Common Error 1**: "Please provide at least one non empty URL!" — The `urls` field contained only blank or whitespace lines. Add at least one valid HTTP/HTTPS URL on its own line and re-run the node.
- **Common Error 2**: Error message like "Request to Agents service failed" with an HTTP status code (e.g., `500 - ...` or `504 - ...`) — The Agents backend could not process the request. Check service health, reduce `max_depth` or the number of URLs, verify network connectivity, and try again later if it was a transient issue.
- **Common Error 3**: Outputs contain messages such as "Page was skipped due to exception" or "Query was skipped due to exception" — The overall run succeeded, but specific pages or queries failed. Inspect the inline message to see the cause (for example 403 Forbidden, timeout, or parsing error) and adjust URLs, depth, or query wording.
- **Common Error 4**: Downstream nodes fail when parsing the `documents` output — Ensure those nodes expect a JSON string and parse it correctly. Avoid manually editing the JSON structure; if necessary, first route the `documents` output through a JSON-aware node to extract or transform fields safely.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/agents/webscraper/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

