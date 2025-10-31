# Dropbox

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves file content from the Carbon data service for a user's Dropbox integration. Given selected Carbon file IDs, it queries the Carbon service and returns a single concatenated string of the matched file contents, each wrapped in a simple <file> block with identifiers. Supports an optional query to further narrow the returned content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorDropbox.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to bring Dropbox-sourced documents into your workflow for downstream processing (for example, as context for LLM prompts or analysis steps). Typically, you first select files via a Carbon file picker or an upstream step that supplies Carbon file IDs. Then pass those IDs here to fetch and return the combined text content.

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">A JSON-encoded array of Carbon file IDs associated with the Dropbox integration. These identify which Dropbox files to fetch.</td><td style="word-wrap: break-word;">[12345, 67890]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional text query to filter or narrow the selection of content returned from the specified files.</td><td style="word-wrap: break-word;">quarterly earnings</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single string containing all fetched file contents, each enclosed in a <file id="..."> block. If the data is chunked, the id may include a chunk suffix.</td><td style="word-wrap: break-word;"><file id="12345"> Document content here... </file> <file id="67890-chunk:0"> Another document chunk... </file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **File IDs must be valid JSON**: Provide file_ids as a JSON array (for example, [12345, 67890]); otherwise, the node will fail to parse.
- **Integration is fixed to Dropbox**: This node specifically targets the Dropbox integration within Carbon.
- **Authorization handled automatically**: salt_user_id is injected by the platform and should not be modified manually.
- **Single concatenated output**: All matched content is combined into one string output, wrapped in simple <file> blocks.
- **Network-dependent**: The node relies on the Carbon service; connectivity or service issues may cause request failures.
- **Timeout behavior**: Requests have a default timeout; large queries or many files may require patience or narrower queries.

## Troubleshooting
- **Invalid JSON in file_ids**: Ensure file_ids is a JSON array string (e.g., [12345, 67890]). Fix formatting if you see a JSON decoding error.
- **Empty or unexpected output**: Confirm the selected file IDs belong to Dropbox and that your optional query is not overly restrictive.
- **Request failed errors**: Check network connectivity and service status. Retry with fewer files or without a query if the request times out.
- **Malformed response errors**: If you see a response decoding error, try again later; ensure the Carbon service is operating normally.
- **Missing authorization**: If access is denied, verify that your session/account is valid so salt_user_id can be injected correctly.
