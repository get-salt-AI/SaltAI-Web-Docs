# GitHub

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector for GitHub. It configures the Carbon integration to ingest or sync data from GitHub based on your project/account settings. Behavior and configuration fields are inherited from the Carbon Data Connector base and specialized for GitHub.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgithub.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull or synchronize data from GitHub into Salt’s Carbon data layer (for example, repositories, issues, pull requests, or related metadata) as part of your data pipelines. Place it where a GitHub source needs to be established before downstream data processing, transformation, or analytics nodes.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The node’s configurable inputs are provided by the Carbon Data Connector base and tailored for GitHub (e.g., authentication, scope, repositories). Exact fields are not specified here.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Produces a handle/reference to the configured GitHub connector or its resulting dataset for use by downstream nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node represents a GitHub data source within the Carbon integration framework; ensure your GitHub account/organization and permissions are correctly configured.
- Authentication and access scopes must allow the node to read the intended GitHub resources (e.g., repos, issues, pull requests).
- Exact input and output schemas are provided by the underlying Carbon Data Connector base and may vary depending on your environment or configuration.

## Troubleshooting
- Missing or insufficient permissions: Confirm the connected GitHub account has the required scopes for the targeted repositories and data types.
- Authentication errors: Verify that the GitHub authentication method configured for your Carbon environment is valid and active.
- No data returned: Check repository names/filters and ensure the resources exist and the account has access.
- Rate limit errors: If you encounter API rate limits, reduce request frequency, narrow the sync scope, or use higher rate-limit credentials.
