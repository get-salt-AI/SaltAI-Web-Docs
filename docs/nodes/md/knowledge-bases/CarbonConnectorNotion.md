# Notion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches and returns data from your Notion workspace using Salt's Carbon data integration. You select specific Notion pages/databases (file IDs) and optionally provide a query to filter or search their contents. The node returns the retrieved data as a string (often JSON-formatted) for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorNotion.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to bring Notion knowledge into your workflow, for example to ground an LLM response or analyze notes and documentation. First, ensure your Notion account is connected via Carbon. Then select the target Notion items through the file picker (file_ids) and optionally add a query to narrow results. Feed the output into processing, analysis, or LLM nodes.

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
<tr><td style="word-wrap: break-word;">query</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter or search query applied against the selected Notion content. Leave empty to fetch default or full content as defined by the integration.</td><td style="word-wrap: break-word;">status = "Open" AND tag CONTAINS "Roadmap"</td></tr>
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">The Notion items to load via Carbon. Uses the Notion integration selector to choose pages/databases you have access to.</td><td style="word-wrap: break-word;">["notion_file_123", "notion_db_456"]</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The retrieved Notion data as a string. Typically JSON or text content, depending on the selected items and query.</td><td style="word-wrap: break-word;">{"results":[{"title":"Q1 Plan","status":"Open"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration required**: You must have the Notion integration connected in Carbon for your account to select and access items.
- **File selection is mandatory**: The node will not run without file_ids. Use the Notion picker to choose pages/databases.
- **Query is optional**: If left blank, the integration's default retrieval behavior applies and may return larger datasets.
- **Permissions enforced**: Returned data respects your Notion and Carbon permissions; inaccessible items will not be fetched.
- **Output format**: The output is a single STRING, often JSON-formatted. Parse it downstream as needed.

## Troubleshooting
- **No results returned**: Verify that selected file_ids are valid and you have access; try simplifying or removing the query.
- **Authorization error**: Ensure your Notion account is connected in Carbon and that salt_user_id is correctly set by the system.
- **Too much data**: Add or tighten the query, or reduce the number of selected Notion items to limit output size.
- **Malformed output parsing**: Treat the output as a string; if JSON is expected, validate and parse it before use.
- **Cannot select files**: Check that the Notion integration is enabled in Carbon and that the workspace/pages are indexed.
