# Google Drive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves text content from Google Drive files via the Carbon data service. You provide a selection of file IDs and an optional query to narrow results; the node fetches matched content and returns it as a single tagged string. Each file's content is wrapped in simple <file> tags for downstream parsing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgoogledrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull Google Drive documents into your workflow for analysis, summarization, or RAG-style prompts. Typically, you select files (or folders) through a prior UI step that supplies the file IDs, optionally add a query to filter chunks or documents, then feed the returned string into a model or processing node.

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">A list of Google Drive file identifiers authorized for the current user, provided as a JSON-encoded array. This is typically populated by a file picker tied to the Google Drive integration.</td><td style="word-wrap: break-word;">["1a2B3cD4eF5g", "7h8I9jK0LmN"]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional search/filter string to narrow which file contents are returned. Leave empty to return all matched content for the selected files.</td><td style="word-wrap: break-word;">project roadmap Q3</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single concatenated string of fetched content. Each item is wrapped as <file id="...">content</file>. Useful for direct ingestion into prompts or further parsing.</td><td style="word-wrap: break-word;"><file id="12345-chunk:0">...file text...</file> <file id="67890">...file text...</file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is specific to the Google Drive integration and will enforce that the provided file IDs belong to that integration.
- file_ids must be valid JSON; improperly formatted input will cause a JSON parsing error.
- The salt_user_id is required but hidden and is automatically injected; do not attempt to supply it manually.
- The node returns a single STRING, not a list. If multiple files are returned, they are concatenated with newlines between <file> blocks.
- Network access to the Carbon service is required; timeouts or service unavailability will cause the node to fail.
- Content is returned as plain text wrapped in simple tags; preserve the tags if downstream nodes rely on them for parsing.

## Troubleshooting
- Error decoding JSON file_ids: Ensure file_ids is a JSON array string (e.g., ["id1", "id2"]).
- No content returned: Confirm that the selected file IDs exist and the query is not over-restrictive. Try removing or simplifying the query.
- Request failed or timed out: Check network connectivity and the availability of the Carbon service; retry later.
- Error decoding JSON response: The service response was not valid JSON. Retry the operation; if persistent, contact support.
- Unexpected permissions error: Verify that the current user has access to the specified Google Drive files and that the integration is properly authorized.
