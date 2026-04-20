# Notion Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Notion Writer creates a new page in Notion from markdown text, handling markdown-to-block conversion and the Notion API call internally. You provide a Notion API token, the parent database or page, a title, the markdown content, and optionally a cover image URL. The node outputs a status string and a JSON-encoded response, which on success contains the created page URL and on failure contains an error description.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/notion-writer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Notion Writer at the end of workflows where you want to persist generated or processed text into Notion, such as AI-generated reports, meeting notes, incident postmortems, or knowledge base articles. Upstream nodes typically include LLM/Text generation nodes (to create the content), formatting or templating nodes (to structure it as markdown), and metadata nodes that define the title or cover image URL. Configure the node with a secure Notion integration token (with access to your target workspace), set parent_type to either "database" or "page" depending on whether you are writing into a database or under a regular page, and provide the corresponding parent_id from Notion. Downstream, the Status and Response outputs can feed into control/branching nodes to handle errors gracefully, or into notification nodes (such as Slack Writer or email sender nodes) to broadcast the new page link. This node is best used in pipelines that standardize on markdown as the document representation and need a simple, reliable way to push that content into Notion.

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
<tr><td style="word-wrap: break-word;">token</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Notion API token for the integration used to create the page. It must belong to an integration that has been granted access to the target database or page and should be provided via a secure mechanism rather than hardcoded.</td><td style="word-wrap: break-word;"><notion-api-token></td></tr>
<tr><td style="word-wrap: break-word;">parent_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The type of Notion parent object under which the new page will be created. Must be either "database" to create a database entry or "page" to create a child page under an existing page.</td><td style="word-wrap: break-word;">database</td></tr>
<tr><td style="word-wrap: break-word;">parent_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Notion ID of the parent object specified by parent_type. When parent_type is "database", this is a database ID; when parent_type is "page", this is a page ID. It must be a valid ID from your Notion workspace that the integration can access.</td><td style="word-wrap: break-word;">b13c2c7e-8af7-4a0f-9e2b-9e6d5f1c8c42</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The title of the new Notion page. This becomes the main page title and, for database parents, typically populates the primary title property shown in database views.</td><td style="word-wrap: break-word;">Weekly Product Intelligence Report – 2026-04-20</td></tr>
<tr><td style="word-wrap: break-word;">content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The markdown-formatted body of the Notion page. It is converted to Notion blocks via the md2notion integration, so it should be valid markdown using standard constructs like headings, lists, paragraphs, and code fences.</td><td style="word-wrap: break-word;"># Summary  This week we shipped the new recommendations engine.  ## Highlights - +12% click-through rate - Reduced latency to 150ms p95  ```sql SELECT * FROM metrics.weekly_performance WHERE week = '2026-04-20'; ```</td></tr>
<tr><td style="word-wrap: break-word;">cover_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL of an image to use as the Notion page cover. If provided, Notion will attempt to fetch and display this image as the page banner; if left empty, the page will have no custom cover.</td><td style="word-wrap: break-word;">https://assets.example.com/reports/weekly-report-cover-2026-04-20.png</td></tr>
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
<tr><td style="word-wrap: break-word;">Status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level indicator of the result of the write operation. It returns "Success" if the page was created correctly, or "Error" if an exception occurred during markdown conversion or the Notion API call.</td><td style="word-wrap: break-word;">Success</td></tr>
<tr><td style="word-wrap: break-word;">Response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded string describing the outcome. On success it contains at least a "url" key with the created Notion page URL; on failure it contains an "error" key with a message describing what went wrong.</td><td style="word-wrap: break-word;">{"url": "https://www.notion.so/workspace/Weekly-Product-Intelligence-Report-2026-04-20-b13c2c7e8af74a0f9e2b9e6d5f1c8c42"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution performs a live HTTP request to the Notion API, so throughput is limited by Notion’s latency and rate limits; avoid firing very large batches without spacing or batching logic.
- **Limitations**: The node assumes markdown input and relies on md2notion; unusual HTML, very complex layouts, or unsupported markdown extensions may not convert cleanly into Notion blocks.
- **Behavior**: On any exception, the node does not crash the workflow; it sets Status to "Error" and returns a JSON Response with an "error" key containing the exception message.
- **Behavior**: The Response output is a plain string containing JSON rather than a structured object, so downstream nodes must parse it to extract fields like the page URL or error message.

## Troubleshooting
- **Invalid parent_type or parent_id**: If Response shows errors about an invalid or missing parent, ensure parent_type is exactly "database" or "page" and that parent_id is a real Notion database or page ID accessible to your integration.
- **Authentication or permission errors**: If the error mentions unauthorized access or invalid token, verify that the token is correct, the integration is enabled, and it has been shared with the target workspace/database/page in Notion.
- **Content conversion failures**: If Status is "Error" and the message references markdown or blocks, simplify the content (remove unusual HTML or extremely long blocks), test with a small snippet, and gradually add sections back to find problematic parts.
- **Rate limiting or network issues**: Timeouts or rate-limit messages from Notion can appear under heavy use. Add delays between executions, reduce batch sizes, or use retry logic in surrounding workflow nodes to make the process more resilient.
