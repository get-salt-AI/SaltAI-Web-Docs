# Notion Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a new Notion page from Markdown content under a specified parent (database or page). It sets the page title, converts Markdown to Notion blocks, and can optionally add a cover image. Returns a success status with the created page URL or an error status with details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Outputs/Writers/Notion Writer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to publish generated or prepared Markdown content into Notion. Provide a Notion integration token with access to the target parent, the parent type and ID, a title, and Markdown content. Optionally include a cover image URL. Typical workflows involve generating content earlier in the pipeline, then passing it here to create a formatted Notion page.

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
<tr><td style="word-wrap: break-word;">token</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Notion API integration token with permission to create pages under the specified parent.</td><td style="word-wrap: break-word;"><notion-integration-token></td></tr>
<tr><td style="word-wrap: break-word;">parent_type</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Type of the parent container where the page will be created. Choose 'database' to create a database entry or 'page' to create a child page.</td><td style="word-wrap: break-word;">database</td></tr>
<tr><td style="word-wrap: break-word;">parent_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ID of the target Notion parent (database_id or page_id) where the new page will be created.</td><td style="word-wrap: break-word;">a1b2c3d4e5f6g7h8i9j0</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Title for the new Notion page.</td><td style="word-wrap: break-word;">Weekly Update - Sprint 14</td></tr>
<tr><td style="word-wrap: break-word;">content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Markdown content to be converted into Notion blocks. Supports headings, lists, code blocks, images, links, inline code, bold/italic, equations, and tables (converted to LaTeX equations).</td><td style="word-wrap: break-word;"># Overview - Task A done - Task B in progress  ```python print('hello') ```</td></tr>
<tr><td style="word-wrap: break-word;">cover_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL of an image to set as the page cover.</td><td style="word-wrap: break-word;">https://example.com/cover.jpg</td></tr>
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
<tr><td style="word-wrap: break-word;">Status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation status: 'Success' if the page was created, otherwise 'Error'.</td><td style="word-wrap: break-word;">Success</td></tr>
<tr><td style="word-wrap: break-word;">Response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with the result. On success: {"url": "<created-page-url>"}. On error: {"error": "<message>"}.</td><td style="word-wrap: break-word;">{"url": "https://www.notion.so/your-workspace/Page-Title-abc123"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication**: The provided token must be an authorized Notion integration with access to the specified parent page or database.
- **Parent type and ID**: Ensure parent_type matches the parent_id (use 'page' with a page_id and 'database' with a database_id).
- **Content format**: The node converts Markdown to Notion blocks. Unsupported Markdown features may be ignored or transformed differently.
- **Cover image**: cover_url should be a publicly accessible URL for Notion to fetch.
- **Response format**: The Response output is a stringified JSON object, not a native object.
- **Rate limits**: Notion API rate limiting may apply and can cause temporary errors.
- **Error handling**: On failure, Status='Error' and Response contains an 'error' message string.

## Troubleshooting
- **Invalid parent_id or parent_type**: Verify the ID corresponds to the selected type ('page' or 'database') and that it exists.
- **Permission denied**: Ensure the integration is added to the target page or database and has the necessary permissions.
- **Invalid token**: Replace token with a valid Notion integration token and confirm it has not expired or been revoked.
- **Cover not applied**: Check that cover_url is reachable over the internet and points to a valid image format.
- **Malformed Markdown**: If rendering is unexpected, simplify the Markdown or remove unusual syntax; verify code block fences and list indentation.
- **API errors or timeouts**: Retry after a short delay to handle transient Notion API issues or rate limits.
