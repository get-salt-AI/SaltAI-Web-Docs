# Notion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects to your Notion integration in Salt and retrieves content from the selected Notion pages/databases. You provide a natural-language query and the specific Notion file IDs to search, and the node returns a text result based on that content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectornotion.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to ground an answer or process against content stored in Notion. Typical workflow: first select or reference the Notion files you have indexed (file_ids), then pass a user query. The node returns a string that you can feed into downstream LLM or processing nodes.

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
<tr><td style="word-wrap: break-word;">query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The question or search text to run against the selected Notion content.</td><td style="word-wrap: break-word;">Summarize the onboarding process from our team handbook.</td></tr>
<tr><td style="word-wrap: break-word;">file_ids</td><td>True</td><td style="word-wrap: break-word;">CARBON_FILE_IDS</td><td style="word-wrap: break-word;">One or more Notion file identifiers to scope the search. Only files from the Notion integration are supported.</td><td style="word-wrap: break-word;">["notion-file-id-1", "notion-file-id-2"]</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Textual result derived from the specified Notion files for the given query.</td><td style="word-wrap: break-word;">The onboarding process includes setting up accounts, reading the team handbook, and completing the first-week checklist.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- You must have your Notion workspace connected and content indexed in Salt before using this node.
- **file_ids** must reference items from the Notion integration; other integrations are not supported by this node.
- If you provide an empty query, the output may be empty or not meaningful.
- The hidden input salt_user_id is auto-managed by the platform and should not be changed.
- Access to private Notion content depends on the permissions configured during integration; the node cannot read content you do not have access to.

## Troubleshooting
- No output or empty result: Verify that query is non-empty and that the referenced file_ids contain relevant content.
- Invalid or unsupported file IDs: Ensure file_ids are from the Notion integration and that those files are indexed.
- Authorization errors: Reconnect the Notion integration or ensure your session is valid so the hidden salt_user_id can authorize the request.
- Results seem incomplete: Narrow your query or reduce the number of file_ids to more specific pages/databases.
- Integration not found: Confirm that the Notion integration is enabled in your workspace settings and that content indexing has completed.
