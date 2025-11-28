# Notion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Search and retrieve content from your connected Notion workspace via Carbon. You provide a natural-language query and select specific Notion pages/databases (file IDs) to scope the search. The node delegates to the Carbon service and returns the matched content as a string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectornotion.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to query knowledge stored in Notion (pages, databases) and feed the retrieved context into downstream reasoning or generation nodes. Typical workflow: authenticate/connect Notion in your Salt account, select Notion files with the file picker, enter a query, then pass the output string to an LLM or another processing step.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The natural-language search query used to find relevant content in the selected Notion files.</td><td style="word-wrap: break-word;">What are the Q3 OKRs and their owners?</td></tr>
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">One or more Notion file identifiers to scope the search. Uses the Carbon file picker configured for the Notion integration.</td><td style="word-wrap: break-word;">["notion-file-id-1", "notion-file-id-2"]</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The search result text returned from Carbon for the specified Notion files and query. Suitable for direct display or as context to downstream nodes.</td><td style="word-wrap: break-word;">Top results from selected Notion pages:\n1) OKRs Q3: ...\n2) Team Assignments: ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- You must have the Notion integration connected to your Salt account before using this node.
- Use the file picker in the file_ids input to ensure you select valid Notion pages/databases that Carbon can access.
- The node returns a string; if you need structured data, process or parse the output downstream.
- Access is performed on behalf of the provided salt_user_id; ensure this user has permission to the selected Notion content.

## Troubleshooting
- No results or empty output: Verify your query is specific enough and that the selected Notion files are indexed and accessible.
- Authorization errors: Ensure your Notion integration is connected and the salt_user_id is valid for this workspace.
- Wrong or missing files in results: Re-open the file picker and confirm you selected the intended Notion pages/databases.
- Output too long: Add more specific query terms or reduce the number of selected files to narrow results.
