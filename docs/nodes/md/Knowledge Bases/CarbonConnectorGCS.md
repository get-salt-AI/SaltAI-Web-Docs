# Google Cloud Storage

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector targeting Google Cloud Storage (GCS). This node configures the integration type as GCS so downstream Carbon data operations can access and synchronize files from your GCS buckets.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Knowledge Bases/CarbonConnectorGCS.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a Carbon data pipeline when you want to connect to content stored in Google Cloud Storage. After adding it, complete the connection flow in the Carbon Connect modal to authorize and select the GCS bucket(s)/path(s). Then route its output into nodes that list, query, or index Carbon-managed files to make GCS content available for search, RAG, or processing.

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
<tr><td style="word-wrap: break-word;">authentication/connection</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Connection details are typically provided via the Carbon Connect modal to authorize access to your GCS account and select buckets or paths.</td><td style="word-wrap: break-word;">User completes OAuth/Key-based setup in the Carbon Connect modal and selects gs://my-bucket/documents/</td></tr>
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
<tr><td style="word-wrap: break-word;">connector</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A Carbon connector handle configured for Google Cloud Storage, used by downstream Carbon file/query/index nodes.</td><td style="word-wrap: break-word;">Connector object referencing the user’s authorized GCS integration and selected scope</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authorization required**: You must authorize the GCS account and select the desired bucket/path via the Carbon Connect modal before this connector can be used.
- **Permissions matter**: Ensure the connected GCS credentials have the necessary read (and optionally write) permissions for the selected buckets/paths.
- **Integration scope**: The files and folders available downstream are limited to the bucket(s)/path(s) selected during connection.
- **Pair with Carbon file/query nodes**: This node establishes the integration; use Carbon file listing, querying, or indexing nodes to actually retrieve or process content.

## Troubleshooting
- **No files found**: Verify the selected bucket/path exists and that your credentials have access. Reopen Carbon Connect and confirm the scope.
- **Authentication failed**: Re-run the Carbon Connect flow to refresh credentials or check if the token has expired.
- **Permission denied errors**: Ask a GCP admin to grant the service account or user the appropriate IAM roles (e.g., Storage Object Viewer) for the target bucket.
- **Unexpected empty results downstream**: Ensure this connector is correctly wired into the downstream Carbon nodes and that those nodes are configured to use this connector’s output.
