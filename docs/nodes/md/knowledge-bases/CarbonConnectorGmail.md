# Gmail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects SaltAI to a Gmail account through the Carbon integrations framework. This node delegates all core behavior (inputs, outputs, syncing, and querying) to the shared Carbon data node base, specialized here to the Gmail integration. It is intended for retrieving or synchronizing Gmail data for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorGmail.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull Gmail data (such as messages or attachments) into a workflow powered by the Carbon service. Typically placed near the start of a pipeline to fetch or index Gmail content, after the corresponding Carbon integration has been authorized and linked to the user account.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the Carbon data base node and the integration’s configuration. This node selects the Gmail integration but does not add custom inputs.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are provided by the Carbon data base node (e.g., data handles or query results from the Gmail integration). This node does not alter the base output schema.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is a thin specialization of the shared Carbon data node and only sets the integration to Gmail.
- A valid Carbon integration connection for Gmail must be configured and authorized for the user before it can return data.
- Behavior such as authentication, data pagination, filters, and return schema is inherited from the Carbon data base node and the Carbon service configuration.

## Troubleshooting
- If no data is returned, ensure the Gmail account is connected and authorized in the Carbon integrations dashboard.
- If authentication errors occur, re-authorize the Gmail integration and ensure any required tokens are valid.
- If the node’s schema (inputs/outputs) appears unclear, refer to the generic Carbon data node documentation for details on expected parameters and result formats.
