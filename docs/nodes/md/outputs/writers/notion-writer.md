# Notion Writer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a new Notion page from Markdown content under a specified parent (database or page). It converts the provided Markdown to Notion blocks, optionally sets a cover image, and returns the resulting page URL or an error. Outputs a simple status string along with a JSON-formatted response string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/outputs/writers/notion-writer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to publish generated content into Notion as a new page. Provide your Notion API token, select whether the parent is a database or a page, supply the parent ID, a title, and Markdown content. Optionally include a cover image URL. This is typically used at the end of a content-generation workflow to store results in Notion.

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
<tr><td style="word-wrap: break-word;">token</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Notion API integration token with permission to create pages under the specified parent.</td><td style="word-wrap: break-word;"><notion-api-token></td></tr>
<tr><td style="word-wrap: break-word;">parent_type</td><td>True</td><td style="word-wrap: break-word;">[["database", "page"]]</td><td style="word-wrap: break-word;">Type of the parent container where the new page will be created. Choose 'database' to create a page under a database, or 'page' to create under another page.</td><td style="word-wrap: break-word;">database</td></tr>
<tr><td style="word-wrap: break-word;">parent_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The ID of the Notion parent (database ID or page ID) where the new page will be created.</td><td style="word-wrap: break-word;">a1b2c3d4e5f6473890abcedf12345678</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Title for the new Notion page.</td><td style="word-wrap: break-word;">Weekly Research Summary</td></tr>
<tr><td style="word-wrap: break-word;">content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Markdown content that will be converted into Notion blocks for the page body.</td><td style="word-wrap: break-word;"># Highlights - Point 1 - Point 2  ## Details Here are the details...</td></tr>
<tr><td style="word-wrap: break-word;">cover_url</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional URL to an image that will be used as the page cover.</td><td style="word-wrap: break-word;">https://example.com/cover.jpg</td></tr>
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
<tr><td style="word-wrap: break-word;">Status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation status string: 'Success' if the page was created, otherwise 'Error'.</td><td style="word-wrap: break-word;">Success</td></tr>
<tr><td style="word-wrap: break-word;">Response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string with the result. On success includes the created page URL; on error includes an error message.</td><td style="word-wrap: break-word;">{"url": "https://www.notion.so/workspace/My-Page-abcdef123456"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication**: The token must belong to a Notion integration that has been invited to the target page or database.
- **Parent type alignment**: Ensure 'parent_type' matches the type of 'parent_id' (database vs page) to avoid errors.
- **Response format**: The 'Response' output is a JSON string, not a structured object. Parse it if you need to read fields.
- **Markdown conversion**: Content is parsed from Markdown into Notion blocks via an internal converter; unsupported Markdown features may be omitted.
- **Dependencies**: Requires the Notion Python client (notion-client) to be installed in the environment.
- **Security**: Never hardcode or share real tokens; use secure secrets handling.

## Troubleshooting
- **Authentication failed**: If Status is 'Error' with an auth-related message, verify the token value, that the integration is enabled, and that it has access to the specified parent.
- **Parent not found or type mismatch**: If you see an object-not-found error, confirm the 'parent_id' is correct and that 'parent_type' matches the object type.
- **Insufficient permissions**: Invite the integration to the target page or database and grant it the necessary permissions.
- **Invalid cover URL**: If the cover fails to set, ensure the URL is reachable and points to a valid image.
- **Rate limits or network errors**: Retry after a short delay. Check network connectivity and Notion API status.
- **Large content issues**: Very long Markdown might hit Notion limits or partial conversion; try splitting content into sections or reducing size.
- **Malformed Markdown**: If conversion fails, simplify or validate the Markdown formatting and try again.
