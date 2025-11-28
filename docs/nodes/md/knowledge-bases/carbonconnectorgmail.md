# Gmail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Configures a Carbon data connector for Gmail. This node identifies the Gmail integration and delegates all processing to the shared Carbon data node base, which handles authentication, configuration, and data retrieval/sync behavior.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgmail.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to ingest or synchronize Gmail content as a data source in your Salt AI workflows. Place it where a Gmail data connection is required for downstream search, retrieval, or enrichment steps. You typically pair it with nodes that manage Carbon service access tokens and with downstream nodes that consume the resulting data handle.

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
<tr><td style="word-wrap: break-word;">configuration</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional parameters for how the Gmail connector should operate (e.g., filters, scopes, or sync options). Exact fields depend on the shared Carbon base implementation.</td><td style="word-wrap: break-word;">{"labels": ["INBOX"], "max_emails": 500}</td></tr>
<tr><td style="word-wrap: break-word;">authorization</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Authentication or token details used by the Carbon service to access Gmail. The base Carbon node typically manages token retrieval and validation.</td><td style="word-wrap: break-word;">{"access_token": "<carbon-jwt-token>"}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A reference or handle to the Gmail-connected data set prepared by the Carbon service, suitable for downstream retrieval or indexing steps.</td><td style="word-wrap: break-word;">{"data_source_id": "gmail-connection-123", "status": "ready"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node strictly targets the Gmail integration; all core behavior (inputs, outputs, and processing) is inherited from the base Carbon data node.
- You must have the Carbon service configured and accessible, and the user/account must have granted Gmail permissions to allow data access.
- Large Gmail mailboxes can take time to index or sync; plan for asynchronous availability and check status before downstream usage.
- Exact input/output schemas are defined by the base Carbon implementation and may vary across versions.

## Troubleshooting
- Authentication errors: Ensure a valid Carbon access token is provided and that the Gmail account is properly authorized.
- Insufficient permissions: Verify that Gmail scopes and account permissions allow reading the necessary labels/folders.
- No data returned: Confirm filters or configuration settings are correct (e.g., labels, date ranges) and that the mailbox contains matching messages.
- Sync delays: For large mailboxes, allow additional time for ingestion. Check job or sync status in the Carbon service logs or monitoring.
