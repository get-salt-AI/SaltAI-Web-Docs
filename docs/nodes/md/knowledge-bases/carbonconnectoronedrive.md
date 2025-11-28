# OneDrive

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for Microsoft OneDrive. It delegates all behavior to the shared Carbon data-connector base, setting the integration to OneDrive so documents and files from a connected OneDrive account can be ingested and used downstream.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectoronedrive.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to connect a OneDrive account as a data source for document ingestion and retrieval within a Salt workflow. Place it early in your pipeline to establish the data connection; subsequent nodes can consume the resulting data handle or dataset to search, retrieve, or process files.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the shared Carbon data connector base. This node only specifies that the target integration is OneDrive.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the shared Carbon data connector base. Typically this is a data handle or dataset reference representing the connected OneDrive source.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration Target**: This node strictly targets the OneDrive integration; it cannot be repurposed for other sources.
- **Authentication Required**: A valid OneDrive authorization (and any required organizational consent) must be completed for access.
- **Permissions Matter**: The connector will only access files the authenticated user or service principal is permitted to read.
- **Ingestion Timing**: Initial syncs and indexing may take time depending on data volume and organizational policies.
- **Service Dependency**: Functionality depends on the Carbon data service and its configuration in your environment.

## Troubleshooting
- **Authentication fails**: Re-run the OneDrive authorization flow and ensure the correct tenant and account are used. Verify any required admin consent has been granted.
- **No files returned**: Confirm the authenticated account has read access to the intended folders/files and that the data has finished syncing/indexing.
- **Rate limits or throttling**: OneDrive API limits can apply. Retry later or reduce the frequency/volume of requests.
- **Permissions errors**: Check OneDrive/Entra ID (Azure AD) app permissions and scopes granted to the integration, and ensure the user/service principal has appropriate access.
- **Service unavailable**: Verify the Carbon service is reachable and healthy in your environment; check network and service status.
