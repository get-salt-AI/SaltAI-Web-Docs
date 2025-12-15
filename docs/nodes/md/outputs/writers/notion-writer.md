# Notion Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a new Notion page from Markdown content. It converts the provided Markdown into Notion blocks and publishes the page under a specified parent (database or page), optionally setting a cover image. Returns operation status and the Notion page URL or an error message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/notion-writer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a content generation workflow to publish AI-generated text as a formatted Notion page. Provide your Notion integration token, select whether the parent is a database or a page, set the parent ID, page title, and Markdown content. Optionally include a cover image URL. The node outputs a success status with the created page URL or an error with details.

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
<tr><td style="word-wrap: break-word;">token</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Notion API integration token with access to the target parent page or database.</td><td style="word-wrap: break-word;"><notion-secret></td></tr>
<tr><td style="word-wrap: break-word;">parent_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of the parent object to create the page under. Choose 'database' to add a page as a database entry or 'page' to nest under a page.</td><td style="word-wrap: break-word;">database</td></tr>
<tr><td style="word-wrap: break-word;">parent_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The ID of the parent Notion database or page where the new page will be created.</td><td style="word-wrap: break-word;">aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Title of the new Notion page.</td><td style="word-wrap: break-word;">Weekly Research Summary</td></tr>
<tr><td style="word-wrap: break-word;">content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Markdown content to be converted into Notion blocks for the page body. Supports headings, paragraphs, lists, images, code blocks, and more.</td><td style="word-wrap: break-word;"># Overview - Item 1 - Item 2  ![Caption](https://example.com/image.png)</td></tr>
<tr><td style="word-wrap: break-word;">cover_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL for the page cover image.</td><td style="word-wrap: break-word;">https://example.com/cover.jpg</td></tr>
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
<tr><td style="word-wrap: break-word;">Status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Indicates whether the operation succeeded or failed.</td><td style="word-wrap: break-word;">Success</td></tr>
<tr><td style="word-wrap: break-word;">Response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized response string. On success, contains the created page URL; on error, contains an error message.</td><td style="word-wrap: break-word;">{"url": "https://www.notion.so/your-page"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The Notion integration token must have permission to access the specified parent page or database.
- Set parent_type correctly: use 'database' to insert into a database, or 'page' to nest under a regular page.
- Content is parsed from Markdown into Notion blocks; some Markdown constructs (e.g., complex tables) may be transformed differently than expected.
- On failure, Status is "Error" and Response contains an error string; on success, Status is "Success" and Response includes {"url": "..."}.
- Ensure the Notion client dependency is installed in your environment (notion-client).

## Troubleshooting
- Error: Invalid or missing token — Verify the token is correct, not expired, and pasted in full. Ensure the integration has been added to the target workspace and shared to the parent page/database.
- Error: Parent not found or access denied — Confirm parent_id is correct (including hyphens) and the integration has access to that parent.
- Success but missing page content — Check that 'content' is valid Markdown. Remove unsupported syntax if rendering is unexpected.
- Cover image not set — Ensure 'cover_url' is a publicly accessible URL; private or blocked URLs may fail to display.
- Received raw error in Response — Inspect the Response string for details; correct the indicated field (e.g., invalid parent_type) and retry.
