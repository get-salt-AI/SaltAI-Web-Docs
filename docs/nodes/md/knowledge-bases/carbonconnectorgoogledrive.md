# Google Drive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches and aggregates file contents from your Google Drive via the Carbon data service. You provide selected Google Drive file IDs, and the node retrieves their processed text and returns a single combined string, preserving per-file boundaries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgoogledrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to bring Google Drive documents into your workflow for downstream analysis or prompting. Typically, first select or obtain file IDs from a prior step, then pass them here to fetch the files’ contents. Optionally add a query to narrow which parts/files to include.

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
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">A JSON-encoded list of Google Drive file IDs to fetch from the Carbon service for the Google Drive integration.</td><td style="word-wrap: break-word;">["1A2B3C_drive_file_id", "4D5E6F_drive_file_id"]</td></tr>
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter/query to narrow the selection or content returned for the specified files.</td><td style="word-wrap: break-word;">title:report AND year:2024</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single string containing the aggregated content of the requested files. Each file’s content is delineated so downstream nodes can parse per-file sections if needed.</td><td style="word-wrap: break-word;"><file id="123">\n...file content...\n</file>\n<file id="456-chunk:0">\n...file chunk content...\n</file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: file_ids must be valid JSON (e.g., a JSON array of strings).
- **Authorization**: salt_user_id is injected automatically and is required for Carbon service access.
- **Filtering**: The optional query can restrict which content is returned; leave blank to fetch all selected files.
- **Output**: Returns a single combined STRING; if files are chunked, chunk indices may be included in the file identifiers.
- **Network dependency**: This node relies on the Carbon service; network or service issues can cause request failures.
- **Timeout**: The request uses a default timeout of approximately 30 seconds.

## Troubleshooting
- **Invalid JSON in file_ids**: Ensure file_ids is a JSON-encoded array (e.g., ["id1", "id2"]).
- **Request failed/timeout**: Check network connectivity and Carbon service availability; retry or reduce the number of files.
- **Empty or missing output**: Verify the provided file IDs exist and are accessible to your account; adjust the query if it filters out all content.
- **Wrong data source**: This node is specific to Google Drive; use the corresponding connector for other sources (e.g., Dropbox, Gitbook, GitHub).
