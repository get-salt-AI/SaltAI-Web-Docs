# OneDrive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects Salt AI to Microsoft OneDrive via the Carbon integration layer. This node selects the OneDrive integration and delegates all connection/authentication to Carbon, enabling access to OneDrive files and folders for downstream data and retrieval workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorOneDrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to ingest or reference content from a Microsoft OneDrive account within a workflow (for example, to search, index, or use documents in retrieval-augmented tasks). Typically placed early in a pipeline to establish the OneDrive data source that subsequent Carbon/knowledge or retrieval nodes will operate on.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the shared Carbon data node base. This node specifically targets the OneDrive integration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the shared Carbon data node base. The output typically represents a reference/handle to connected OneDrive content for downstream nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is a thin selector around the Carbon OneDrive integration; authentication and permissions are handled by Carbon.
- Your organization must have the OneDrive integration enabled in Carbon, and the user must grant appropriate permissions to access files and folders.
- The visible content and indexing scope depend on the permissions granted during the connection flow.
- If your workflow includes search or retrieval over OneDrive files, ensure appropriate downstream nodes are configured to query Carbon-managed content.

## Troubleshooting
- Authentication prompts do not appear: Ensure Carbon is reachable and the OneDrive integration is enabled for your organization.
- No files found after connecting: Verify the connected OneDrive account has accessible files and that permissions were granted for the correct folders.
- Access denied or partial content: Confirm the user granted the required scopes and has permission to the target files/folders in OneDrive.
- Downstream nodes fail to use the connection: Ensure the downstream nodes are compatible with Carbon data references and are configured to query the connected source.
