# Google Cloud Storage

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches content from files connected through the Carbon Google Cloud Storage integration. Given selected Carbon file IDs, it queries the Carbon service and returns a single concatenated text output. Each file’s content is wrapped with a simple <file id="..."> tag for easy downstream parsing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgcs.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after users have connected their GCS content via the Carbon connection flow and selected files. Provide the Carbon file IDs (produced by the file picker/connector UI) to retrieve the file contents as a single text block for further processing in your workflow (e.g., search, summarization, or LLM prompts).

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">A list of Carbon file identifiers associated with the Google Cloud Storage integration. This should be a JSON-encoded array of file IDs provided by the Carbon connection/file selection UI.</td><td style="word-wrap: break-word;">[12345, 67890]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional query to narrow down the selection of content returned from the provided files (e.g., keyword filter). If empty, full file contents are returned.</td><td style="word-wrap: break-word;">error logs from last week</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single concatenated text string containing the fetched file contents. Each file is wrapped as: <file id="<id>">\n<content>\n</file>. If files are chunked, the id may include a -chunk:<index> suffix.</td><td style="word-wrap: break-word;"><file id="12345">\n...file content...\n</file>\n<file id="67890">\n...file content...\n</file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The file_ids input must be valid JSON representing an array of Carbon file IDs produced by the Carbon connection/file-selection UI.
- This node is scoped to the Google Cloud Storage integration; ensure the selected files originate from GCS.
- The salt_user_id input is automatically injected by the platform; do not override it manually.
- If you provide a query, the Carbon service may return only matching portions or files; leave it empty to retrieve full content.

## Troubleshooting
- JSON decode error for file_ids: Ensure file_ids is a valid JSON array string (e.g., [12345, 67890]) and not a raw string or malformed JSON.
- No content returned: Verify the selected file IDs are correct, the files are accessible to the user, and the Carbon connection to GCS is active.
- Permission or authentication errors: Make sure the user is properly authenticated and has a valid Carbon connection to Google Cloud Storage.
- Timeouts or request failures: Try again later, reduce the number of files requested, or narrow results with the query to limit response size.
