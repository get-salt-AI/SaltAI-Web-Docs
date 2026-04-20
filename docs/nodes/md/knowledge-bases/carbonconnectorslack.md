# Slack

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node binds a Carbon data pipeline to the Slack integration defined in the Carbon integrations module. It selects the Slack integration type and delegates configuration, authentication, and data transfer to shared BaseCarbonDataNode logic. Use it whenever you need Slack as an input or output inside a Carbon-driven workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorslack.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Slack node when you want your Carbon-powered workflows to interact with a Slack workspace, either to ingest Slack content (such as channel messages, incident updates, or discussion threads) or to push information back into Slack (such as alerts, summaries, or workflow notifications). In a typical pipeline, workspace-level Slack credentials and app configuration are managed centrally in the Carbon integration settings; this node then references that configuration by specifying the Slack integration. Upstream, it commonly follows scheduling, trigger, or configuration nodes that determine when and what to sync. Downstream, its standardized Carbon dataset output is usually consumed by analysis and processing nodes (for example summarization, classification, enrichment, or routing to other services) or by other connector nodes if you are fan-outing results. Because it is a thin wrapper over the shared BaseCarbonDataNode, you should treat it similarly to other CarbonConnector variants: define integration details and security centrally, keep scopes minimal, and design queries or scopes that avoid pulling unnecessary Slack history.

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
<tr><td style="word-wrap: break-word;">integration_config</td><td>False</td><td style="word-wrap: break-word;">DICT</td><td style="word-wrap: break-word;">Optional per-node options for the Slack integration, such as which channels to target, whether to include threads, limits on messages, or date and time windows. If not provided, the node uses the default configuration defined in the Carbon Slack integration settings. Exact keys and behavior depend on how your Carbon Slack integration is configured.</td><td style="word-wrap: break-word;">{'workspace': 'engineering_workspace', 'channels': ['C03PRODINCIDENTS', 'C02ONCALL'], 'include_threads': True, 'max_messages': 200, 'time_range': {'from': '2026-04-19T00:00:00Z', 'to': '2026-04-20T00:00:00Z'}}</td></tr>
<tr><td style="word-wrap: break-word;">auth_reference</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference to a stored Slack connection or credential profile managed by Carbon integrations. Use this if you have multiple Slack workspaces or apps and need this node to use a specific one instead of the global default. The reference must match a configured Slack integration entry.</td><td style="word-wrap: break-word;">slack_prod_workspace_bot</td></tr>
<tr><td style="word-wrap: break-word;">query_or_scope</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level query, label, or scope that the underlying Slack integration interprets to decide what data to fetch or where to send data. This might represent a logical query or a structured convention combining channels and time windows, depending on your Carbon setup.</td><td style="word-wrap: break-word;">scope:recent_incidents channel:#prod-incidents last:24h</td></tr>
<tr><td style="word-wrap: break-word;">trigger</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Generic trigger input primarily used for orchestration or flow control. It can be any object that signals when this node should execute, such as a scheduler tick, manual trigger payload, or a completion event from an upstream step. The content is typically not used as Slack data itself.</td><td style="word-wrap: break-word;">{'event': 'nightly_sync', 'run_id': 'sync-2026-04-20-01-00', 'timestamp': '2026-04-20T01:00:00Z'}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">CARBON_DATASET</td><td style="word-wrap: break-word;">A Carbon-standard dataset object populated with Slack-derived data or operation results. The internal structure follows the Carbon data container conventions, with fields representing Slack content such as messages, channels, users, timestamps, and any integration-specific metadata. Downstream nodes can treat this like any other Carbon dataset for analysis, transformation, or further routing.</td><td style="word-wrap: break-word;">{'source': 'slack', 'workspace': 'engineering_workspace', 'items': [{'id': '1713590400.000200', 'type': 'message', 'channel_id': 'C03PRODINCIDENTS', 'channel_name': 'prod-incidents', 'user_id': 'U01ONCALL', 'username': 'oncall-engineer', 'text': '[INC-2045] Elevated error rates in api-gateway, investigating.', 'ts': '1713590400.000200', 'thread_ts': '1713590400.000200', 'metadata': {'reactions': [{'name': 'warning', 'count': 3}], 'is_thread_root': True}}], 'meta': {'integration': 'SLACK', 'fetched_at': '2026-04-20T01:01:05Z', 'cursor': 'g3Nlc2N1cnNvcl9uZXh0X3BhZ2U6Mg=='}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large historical fetches across many high-traffic channels can be slow and may require multiple paginated calls through the underlying Slack API; constrain channels and time windows where possible to keep runs efficient.
- **Limitations**: The node can only perform operations that the Carbon Slack integration supports; if your Slack app or Carbon configuration does not expose a particular Slack API feature, you will not be able to access it from this node.
- **Behavior**: Authentication, token refresh, workspace selection, and low-level error handling are centralized in the Carbon integrations layer; this node primarily selects the Slack integration type by returning the SLACK constant from CARBON_INTEGRATIONS.
- **Behavior**: Because the node inherits behavior from BaseCarbonDataNode, it will share common behaviors with other CarbonConnector nodes, such as standardized dataset formats, logging patterns, and error reporting, which may influence how errors and statuses surface in your workflows.

## Troubleshooting
- **Authentication failures (for example, 'invalid_auth' or 'not_authed')**: Verify that the Slack app is correctly configured in the Carbon integration settings and that any auth_reference you pass matches a valid configured Slack connection. Ensure tokens or OAuth credentials stored in Carbon are still valid.
- **Permission or channel errors (for example, 'channel_not_found' or 'not_in_channel')**: Confirm that the configured Slack app is installed in the correct workspace, has the necessary scopes, and is a member of the target channels defined in your integration_config or query_or_scope.
- **Empty or unexpectedly small datasets**: If the node returns an empty result, double-check your filters in integration_config or query_or_scope, and validate that there is actual Slack activity matching those filters within the target timeframe.
- **Rate limit or throttling issues**: If runs intermittently fail or slow down due to Slack rate limiting, reduce the volume of data requested in each run, increase the interval between runs, or split heavy syncs into multiple scheduled workflows.
