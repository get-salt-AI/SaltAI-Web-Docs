# Dropbox

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Connects your Dropbox account to Salt’s Carbon data layer. This node delegates to the Carbon Dropbox integration so you can authorize access, browse, and select Dropbox content for downstream use in workflows. It standardizes how Dropbox files are referenced and retrieved through Carbon.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectordropbox.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to bring files or folders from Dropbox into a Salt workflow via the Carbon platform. Typically, place it early in a data ingestion pipeline to authenticate, select content, and pass a Carbon-managed reference to subsequent processing nodes (e.g., parsing, chunking, embedding, or search).

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The node inherits its inputs from the Carbon base data node. Exact input fields (e.g., user/account context, file selection, filters) are not specified in the available source.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Emits a Carbon-managed reference to the selected Dropbox content and/or associated metadata for downstream nodes. Exact output fields and types are not specified in the available source.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication required**: You must authorize Dropbox access via the Carbon connection flow before the node can list or retrieve content.
- **Permissions apply**: The node only accesses files permitted by the connected Dropbox account and granted scopes.
- **Integration targeting**: This node is bound to the Dropbox integration; it will use Dropbox as the content source within Carbon.
- **Indexing/availability**: Newly connected or updated files may require some time before they become fully searchable or retrievable depending on Carbon’s sync/indexing status.
- **Environment dependency**: The node relies on the Carbon service being reachable and properly configured in your environment.

## Troubleshooting
- **No files visible after connecting**: Ensure the Dropbox account was successfully authorized and has the expected files. Reopen the connection modal and verify scopes and selected folders.
- **Authentication fails**: Check that Carbon service is reachable and that your token fetcher or JWT creation flow is configured. Re-authenticate Dropbox via the Carbon connection UI.
- **Missing or delayed content**: Allow time for Carbon to sync/index the Dropbox content. If delays persist, re-trigger a refresh or reconnect.
- **Permission errors**: Confirm the Dropbox account has access to the files/folders you’re trying to use and that the integration scopes grant read access.
- **Workflow errors downstream**: Verify downstream nodes support the Carbon content reference emitted here and are configured to read from Carbon-backed sources.
