# Intercom

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for Intercom. This node identifies the Intercom integration to the Carbon backend and delegates all connector behavior (authentication, configuration, syncing, and data access) to the shared Carbon data connector base.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorintercom.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to bring Intercom conversations, contacts, or related objects into Salt via Carbon. Place it at the point in your workflow where you establish or reference an Intercom connection, then pass its output to downstream nodes that handle indexing, syncing, search, or RAG over Carbon-managed data.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs for this node are defined by the shared Carbon data connector base. They typically cover connection/auth configuration, workspace or app selection, and sync options, while this node itself only specifies that the integration type is Intercom.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">connection</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A Carbon connector reference targeting the Intercom integration. This reference is meant to be passed into downstream nodes that operate on Carbon data sources (e.g., sync, query, or index nodes).</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration selection**: This node’s primary role is to select the Intercom integration for a Carbon connector; all the heavy lifting (auth flows, configuration, data fetching) is implemented by the shared Carbon data connector layer.
- **No direct data operations**: The node does not itself fetch, index, or query Intercom data. It only produces a connector reference to be consumed by other nodes.
- **Prerequisites**: Intercom must be enabled as an integration in your Carbon environment, and the user configuring the node must have permissions to authorize the relevant Intercom workspace/application.
- **Configuration handling**: Any OAuth or API-key based authentication, account selection, and sync configuration is typically managed through the Carbon connection UI or base connector configuration, not as explicit fields unique to this node.

## Troubleshooting
- **Cannot authenticate or connect to Intercom**: Verify that Intercom is enabled in your Carbon environment and that you have permission to authorize the Intercom workspace/app. Check that any required credentials are valid and correctly configured in the Carbon integration settings.
- **Downstream node reports unsupported source**: Ensure that the downstream node is designed to accept a Carbon connector reference and supports Intercom as a valid source type.
- **No Intercom data appearing after setup**: Confirm that the relevant Intercom workspace has conversations/objects accessible via the authorized account and that any required initial sync or indexing job has completed in Carbon.
- **Multiple Intercom workspaces or apps**: If your organization uses more than one Intercom instance, make sure the correct one is selected in the underlying Carbon connector configuration or connection UI.
