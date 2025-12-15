# OneDrive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects to your OneDrive account through Salt AI’s Carbon integration. This node serves as a data connector, enabling authenticated access to browse or sync files and pass them downstream in your workflow. It relies on a shared Carbon integration layer for authentication and data retrieval.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectoronedrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to ingest or reference content stored in OneDrive within a workflow. Typically, you will first authenticate your OneDrive account via the Carbon connection modal, then use this node to expose files or file references that can be processed by subsequent nodes (e.g., document parsers, embedders, or analysis nodes).

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
<tr><td style="word-wrap: break-word;">connection</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The authenticated OneDrive connection or account context to use. If not provided, you may be prompted to connect via the Carbon modal.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">path_or_scope</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional folder path or scope within OneDrive to target for listing or fetching files.</td><td style="word-wrap: break-word;">/Shared Documents/Reports</td></tr>
<tr><td style="word-wrap: break-word;">filters</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional filters to narrow down which files are returned (e.g., file types, updated-after timestamps).</td><td style="word-wrap: break-word;">fileType=pdf; updatedAfter=2024-01-01</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The OneDrive-sourced data or file references retrieved via Carbon. This is intended to be consumed by downstream nodes for parsing, indexing, or analysis.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Authentication is required: you must connect your OneDrive account using the Carbon connection flow before this node can access content.
- Access is permission-bound: results depend on the permissions of the connected OneDrive account within your organization/tenant.
- This node maps to the OneDrive integration type internally; behavior for listing, filtering, and retrieval is standardized across Carbon connectors.
- If your workflow expects specific data formats, ensure downstream nodes support the structure returned by Carbon for OneDrive content.

## Troubleshooting
- No files appear: Confirm your OneDrive connection is established and that the account has access to the specified path or shared folders.
- Authentication prompts repeatedly: Sign out and reconnect your OneDrive account via the Carbon modal, then rerun the workflow.
- Unexpected empty results with filters: Remove or relax filters (file type, date range) to verify content is discoverable.
- Permission errors: Verify you have the necessary OneDrive/SharePoint permissions and that the connection uses the correct account.
