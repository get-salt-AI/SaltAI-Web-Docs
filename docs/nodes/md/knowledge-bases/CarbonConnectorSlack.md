# Slack

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Establishes a secure connection between Salt and Slack through Carbon’s integrations. This node configures and authorizes the Slack integration, enabling your workspace to ingest and use Slack content (messages, files, threads) in downstream steps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorSlack.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to connect a Slack workspace so its content can be indexed and made available for search, retrieval, or analysis within your workflow. Typically placed early in a pipeline before nodes that browse, query, or process connected Carbon data sources.

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
<tr><td style="word-wrap: break-word;">authentication</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Initiates and stores the authorization required to access Slack via Carbon. Usually handled through an OAuth-style connect flow.</td><td style="word-wrap: break-word;">Connect Slack account</td></tr>
<tr><td style="word-wrap: break-word;">workspace_selection</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Option to select or confirm which Slack workspace to connect if multiple are available for the user.</td><td style="word-wrap: break-word;">acme-inc.slack.com</td></tr>
<tr><td style="word-wrap: break-word;">sync_scope</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Controls which Slack resources to sync (e.g., channels, threads, files). The exact options depend on your Carbon configuration and Slack permissions.</td><td style="word-wrap: break-word;">Public channels, selected private channels</td></tr>
<tr><td style="word-wrap: break-word;">filters</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional filters for limiting synced data by channel, date range, or other attributes.</td><td style="word-wrap: break-word;">Channels: #product, #eng; Date range: last 90 days</td></tr>
<tr><td style="word-wrap: break-word;">reindexing_behavior</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Settings related to initial sync and periodic re-sync, including whether to perform a full or incremental update.</td><td style="word-wrap: break-word;">Incremental sync every 24 hours</td></tr>
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
<tr><td style="word-wrap: break-word;">connection</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A handle or reference to the established Slack integration in Carbon, used by downstream nodes to query or fetch content.</td><td style="word-wrap: break-word;">carbon_connection_reference</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">High-level status of the connection (e.g., connected, pending authorization, failed).</td><td style="word-wrap: break-word;">connected</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node specifically targets the Slack integration in Carbon; the integration type is fixed to Slack.
- Slack authorization may require workspace admin approval depending on your organization’s policies.
- The amount and type of data available (public channels, private channels, DMs) is governed by granted permissions and Carbon’s configured scopes.
- Initial syncs can take time depending on workspace size and selected scope; downstream nodes may not see data until indexing completes.
- Ensure your environment is configured to reach the Carbon service and that the user has a valid Carbon token.

## Troubleshooting
- Authorization failed: Re-run the connect flow and ensure you are signed in to the correct Slack workspace with required permissions.
- No data appears after connecting: Verify sync scope and filters, then allow time for initial indexing to complete. Check connection status in your Carbon dashboard.
- Missing private channels or DMs: Confirm the integration was granted the necessary permissions and that access to those resources is allowed by your Slack admins.
- Intermittent errors or rate limits: Reduce sync scope or frequency and try again later; Slack APIs enforce rate limits.
- Downstream nodes report missing source: Ensure this Slack connection output is correctly wired into nodes that read from Carbon data sources.
