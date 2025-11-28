# Slack

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Provides a Carbon data connector for Slack. It identifies Slack as the target integration and delegates all configuration, authentication, and synchronization behavior to the shared Carbon data-connector base. Use it to bring messages and files from eligible Slack workspaces/channels into Salt’s Carbon data pipeline.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorslack.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to ingest or synchronize Slack content into your Carbon-managed datasets. Place it wherever your workflow selects/configures a data source before a sync, crawl, or indexing step that consumes a Carbon connector. This node is typically paired with subsequent Carbon operations (e.g., start sync, check status, index content) to complete the end-to-end ingestion flow.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">All input handling is provided by the shared Carbon connector base. This node’s only specialization is selecting Slack as the integration type.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs whatever the shared Carbon connector base provides for data connectors (e.g., a configured connector object or parameters for downstream Carbon nodes).</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node selects Slack as the Carbon integration. All behavior (inputs, outputs, validation, and execution) is defined by the shared Carbon data connector base.
- Ensure your Slack workspace and channels are accessible and that the required permissions are granted in Slack before attempting a sync.
- You may need to complete an OAuth or token-based authorization flow outside this node, depending on your organization’s configuration.

## Troubleshooting
- Authentication fails: Verify that your Slack app is installed in the target workspace and that the required scopes/permissions are granted. Reauthorize if needed.
- No data appears after running: Confirm channel access permissions, message retention policies, and that the configured workspace/channel IDs are correct.
- Permission errors: Check Slack admin policies and ensure the account or app used for authorization can read the intended channels and files.
