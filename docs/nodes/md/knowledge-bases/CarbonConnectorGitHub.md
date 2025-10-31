# GitHub

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for GitHub. It identifies the integration type as GitHub and produces a handle that downstream Carbon data nodes can use to sync or process content from GitHub.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/CarbonConnectorGitHub.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node at the start of a Carbon data workflow when you intend to source data from GitHub. Connect its output to subsequent Carbon data operations (e.g., nodes that query, sync, or transform content) to enable GitHub as the data source.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">This connector does not define custom inputs beyond what is inherited from the base Carbon data node.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">connector</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A Carbon integration handle scoped to GitHub, to be consumed by downstream Carbon data nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration scope**: This node only selects the GitHub integration; any actual fetching, indexing, or processing is performed by downstream Carbon data nodes.
- **Authorization required**: Ensure your workspace has valid GitHub authorization and necessary permissions configured before attempting to use this connector in a workflow.
- **Compatibility**: Use this node with other Carbon data nodes that accept a Carbon connector handle as input.

## Troubleshooting
- **No data appears downstream**: Verify that your GitHub authorization is properly configured in your environment and that downstream nodes are set up to use the connector handle.
- **Permission errors**: Confirm the connected GitHub account has access to the repositories or organizations you intend to use.
- **Unexpected node error**: Recreate the connector and ensure downstream nodes are compatible with Carbon connectors and correctly wired.
