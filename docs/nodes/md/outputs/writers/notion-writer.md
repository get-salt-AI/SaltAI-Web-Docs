# Notion Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Notion Writer creates a new Notion page from plain text or markdown content using a Notion integration token. You specify a parent database or page, a title, and the content body, and the node converts the markdown to Notion blocks and calls the Notion API. It returns a simple status string and the raw JSON response (as a string), which includes the created page URL on success or an error description on failure.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/notion-writer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Notion Writer to automatically store AI-generated content, notes, or reports into Notion as part of a Salt pipeline. Typical workflows include: sending LLM-generated meeting summaries into a team knowledge database, logging experiment runs under a lab notebook page, or creating structured documentation pages from templates. Upstream nodes usually generate or assemble the text (for example, a chat model node or a summarization node) and pass it into the `title` and `content` inputs. Downstream, the `Response` output can be parsed to extract the created page `url`, logged for auditing, or forwarded to notification nodes so users can quickly open the new page.

Choose `parent_type = "database"` when you want each run to create a new database entry in a specific Notion database (using its database ID), or `parent_type = "page"` to create a standalone child page under an existing page. Ensure the `token` corresponds to a Notion integration with access to the target workspace object and that the integration has been added to the relevant database or page in Notion. For best results, pass well-structured markdown into `content` (headings, bullet lists, code blocks) and use `cover_url` when you want visually distinctive pages, such as dashboards or executive summaries.

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
<tr><td style="word-wrap: break-word;">token</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Notion API integration token for authenticating requests. It must come from a Notion internal integration that has access to the specified parent database or page.</td><td style="word-wrap: break-word;"><notion-api-token></td></tr>
<tr><td style="word-wrap: break-word;">parent_type</td><td>True</td><td style="word-wrap: break-word;">["database", "page"]</td><td style="word-wrap: break-word;">Indicates whether `parent_id` refers to a Notion database or a page. Use `database` to create new entries in a database; use `page` to create a child page under a specific parent page.</td><td style="word-wrap: break-word;">database</td></tr>
<tr><td style="word-wrap: break-word;">parent_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Notion ID of the parent object. For `database`, provide the database ID; for `page`, provide the parent page ID. You can use either the raw UUID or the standard hyphenated Notion ID format.</td><td style="word-wrap: break-word;">b3f0a2e4-1234-5678-9abc-d0e1f2345678</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Title of the new Notion page or database entry. This is what users see as the page name in Notion.</td><td style="word-wrap: break-word;">Weekly Product Sync – 2026-04-27</td></tr>
<tr><td style="word-wrap: break-word;">content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Body content of the Notion page, written as plain text or markdown. It is converted to Notion blocks internally, supporting standard markdown constructs like headings, lists, and code blocks.</td><td style="word-wrap: break-word;"># Meeting Summary  **Attendees:** Alex, Jordan, Priya  - Decisions:   - Launch v2 to beta users next sprint - Action items:   - @Alex finalize rollout plan   - @Priya update documentation </td></tr>
<tr><td style="word-wrap: break-word;">cover_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL for the page cover image. This should be a publicly accessible image URL to ensure Notion can fetch and display it reliably.</td><td style="word-wrap: break-word;">https://assets.example.com/covers/product-sync-cover.png</td></tr>
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
<tr><td style="word-wrap: break-word;">Status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level status of the write operation. Typically returns `Success` if the Notion page was created, or `Error` if an exception occurred during conversion or API communication.</td><td style="word-wrap: break-word;">Success</td></tr>
<tr><td style="word-wrap: break-word;">Response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized response object from the Notion write process. On success, it includes a `url` field for the created page; on failure, it contains an `error` field describing what went wrong. Downstream nodes should parse this string as JSON if they need structured fields.</td><td style="word-wrap: break-word;">{"url": "https://www.notion.so/Workspace/Weekly-Product-Sync-2026-04-27-b3f0a2e4123456789abcd0e1f2345678"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes an external HTTP request to Notion. Latency depends on network conditions and content size, so expect up to a few seconds for large pages.
- **Limitations**: Notion imposes limits on page size and block counts. Extremely long markdown documents or very large embedded content may fail or be truncated during conversion.
- **Behavior**: Any exception (invalid token, bad parent ID, markdown conversion failure) is captured and returned as `Status = "Error"` with an `error` message inside the JSON-encoded `Response`.
- **Behavior**: The node always returns JSON as a string in `Response` rather than a structured object. Consumers that need the page URL or error details must parse the string themselves.

## Troubleshooting
- **Authentication or permission errors**: If `Status` is `Error` and `Response` contains messages about an invalid key or insufficient permissions, verify that the `token` belongs to a Notion integration added to the target workspace and explicitly granted access to the `parent_id` object.
- **Invalid parent_id or wrong parent_type**: Errors mentioning an unknown or unsupported parent usually mean `parent_id` is incorrect or `parent_type` does not match the actual object. Confirm the database or page ID in Notion and ensure you selected `database` vs `page` appropriately.
- **Content conversion or size issues**: If the error suggests payload or block limits, reduce `content` size or split it into multiple pages. Remove unusual markdown constructs that the converter might not support well.
- **Missing or unexpected URL in Response**: If `Status` is `Success` but you cannot find the expected `url` field, log and inspect the raw `Response` string to see the exact structure, then adjust any downstream JSON parsing or assumptions accordingly.
