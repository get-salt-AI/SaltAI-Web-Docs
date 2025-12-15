# GitHub

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Establishes a connection to the GitHub integration through Carbon. This node delegates all core behavior to a shared Carbon data connector base, enabling authorization and data synchronization for GitHub resources under a unified pattern.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgithub.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to link GitHub as a data source within a Carbon-powered workflow. It is typically placed at the start of a data ingestion or synchronization pipeline to authenticate and prepare access to GitHub content before downstream processing.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The specific configuration and fields required by this node are defined by the Carbon data connector base and the GitHub integration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The output produced by this node follows the standard Carbon data connector output for integrations and is used by downstream nodes to continue the workflow.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration type**: This node is specifically wired to the GitHub integration in Carbon.
- **Authentication**: Authorization and token handling are managed via the shared Carbon data connector base; ensure your environment has access to the Carbon service.
- **Permissions**: The connected GitHub account or token must have sufficient permissions for any repositories or organizations you intend to access.
- **Security**: Do not paste real secrets or tokens directly; use secure credential mechanisms provided by your environment.
- **Behavior**: Actual inputs/outputs and UI options are inherited from the Carbon base node; consult your platform’s Carbon connector documentation for exact fields.

## Troubleshooting
- **Authentication failed**: Verify your Carbon service is reachable and that the GitHub authorization has been completed successfully.
- **Insufficient permissions**: Ensure the GitHub account or app used has the required scopes (e.g., read access to repositories) and that repository/org permissions are granted.
- **Resource not found**: Double-check repository or organization identifiers (owner/repo names) and visibility (private/public) settings.
- **Rate limiting or throttling**: If syncing large repositories or many resources, monitor GitHub API rate limits and adjust scheduling or scopes accordingly.
