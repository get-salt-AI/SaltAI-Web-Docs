# Slack

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for Slack. This node identifies the Slack integration to the Carbon backend and delegates all connector behavior (authentication, configuration, and data access) to the shared Carbon data connector logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorslack.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to connect a Slack workspace as a data source for downstream Carbon-powered operations (such as indexing, syncing, or querying Slack content). Place it where your workflow needs to establish or reference a Slack connection before handing the output to nodes that perform data ingestion or retrieval.

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
<tr><td style="word-wrap: break-word;">connection</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A Carbon connector reference targeting the Slack integration, suitable for passing into downstream nodes that operate on Carbon data sources.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration selection**: This node specifically targets the Slack integration in Carbon and relies on shared base functionality for authentication and data access.
- **No direct data operations**: The node itself does not fetch or index data; it provides a connector reference that downstream nodes can use.
- **Prerequisites**: Slack must be enabled/allowed in your Carbon environment and the user must have the appropriate permissions to connect a Slack workspace.
- **Configuration handling**: Any required authentication or account selection flows are managed by the underlying Carbon connector layer, not by this node’s own inputs.

## Troubleshooting
- **Cannot authenticate or connect**: Ensure Slack integration is enabled in your Carbon environment and that you have valid permissions to authorize the workspace.
- **Downstream node errors**: Verify that the downstream node accepts a Carbon connector reference and supports Slack as a source.
- **No data available after connection**: Confirm that the Slack workspace and channels you've authorized contain accessible content and that the Carbon service has completed any required syncing.
