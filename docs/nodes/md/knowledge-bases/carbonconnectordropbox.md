# Dropbox

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects your Dropbox account to Salt’s Carbon data layer. This node delegates to the Carbon integration for Dropbox to authenticate and surface your Dropbox files for selection and downstream use.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectordropbox.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to bring content from Dropbox into your workflow via Carbon. Typically, you add this connector, authenticate/authorize Dropbox through the Carbon connection UI, select folders/files to include, and then pass the connected content to downstream nodes that consume Carbon-managed data.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the base Carbon data node. They generally relate to Carbon connection/configuration and file selection.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the base Carbon data node and typically represent selected/linked content references from Dropbox managed by Carbon.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is a typed connector for the Dropbox integration within Carbon; its behavior (inputs/outputs) is inherited from the base Carbon data node.
- You must have appropriate Dropbox access and complete the Carbon connection flow to see and select files.
- Display name appears as "Dropbox" in the node palette.

## Troubleshooting
- If you cannot see Dropbox content, ensure you have completed the Carbon connection and granted the required Dropbox permissions.
- If authentication fails or expires, re-open the connection dialog and re-authorize Dropbox.
- If selected files are not appearing downstream, verify the downstream node expects Carbon-managed content and that the selection is saved/applied in the connection UI.
