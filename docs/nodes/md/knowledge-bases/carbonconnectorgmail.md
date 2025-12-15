# Gmail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects your workspace to a Gmail account via the Carbon integration. This node designates Gmail as the data source and relies on shared Carbon logic to handle authentication, syncing, and data retrieval.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgmail.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to ingest or reference Gmail content (emails, threads, and possibly attachments) through the Carbon data layer. Place it at the start of a pipeline that needs Gmail data, then connect its outputs to downstream nodes that process, index, or analyze the retrieved content.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the base Carbon data node and may include authentication context, scope, and optional filters for sync. This Gmail node only selects the Gmail integration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the base Carbon data node and typically include a reference or handle to the synced Gmail dataset or data stream for downstream processing.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node sets the integration to Gmail; all behavior (inputs/outputs) is inherited from the base Carbon data node.
- Authentication and authorization are required; ensure the user or workspace has completed Gmail OAuth within the Carbon layer.
- Sync behavior (full, incremental, filters) is controlled by the base Carbon node configuration and/or platform settings, not by this node directly.
- Email and attachment access depend on account permissions and organizational policies.

## Troubleshooting
- Authentication errors: Reconnect the Gmail account through the platform’s Carbon connectors and ensure OAuth is valid.
- No data returned: Verify the connected Gmail account has accessible mail, check sync filters/range, and confirm the sync completed.
- Permission or scope issues: Confirm the OAuth scopes requested by the integration are granted and that the account/labels meet the filter criteria.
- Rate limits or throttling: If syncs stall or slow down, retry later or reduce the volume of requested data.
