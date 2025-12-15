# S3

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for Amazon S3. This node acts as a thin integration selector that tells the Carbon pipeline to use the S3 connector; all common behavior (auth, configuration, syncing, and data retrieval) is handled by the shared Carbon data connector base logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectors3.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to connect and sync documents from an Amazon S3 bucket into Salt for downstream search, RAG, or analytics. Place it where your workflow collects or updates external data; pair it with nodes that trigger connection/auth flows and nodes that consume the resulting dataset or files.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs for this node are defined by the shared Carbon data connector base. This node only specifies the S3 integration type; the base layer manages actual parameters (e.g., auth, scopes, sync controls).</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are provided by the shared Carbon data connector base and typically represent a configured connector/dataset reference for downstream nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration selector**: This node only selects the S3 integration; the shared Carbon data connector base handles inputs, authentication, syncing, and actual data retrieval.
- **Authentication required**: You must complete authentication/authorization for S3 in the Carbon connection flow before data can be accessed.
- **Permissions**: Ensure the S3 credentials used have at least list and read permissions on the bucket/prefix you intend to index.
- **Large datasets**: Initial syncs on large buckets or wide prefixes can take time; schedule accordingly.

## Troubleshooting
- **Authentication fails**: Re-run the Carbon connection flow and confirm credentials are valid and not expired.
- **No files returned**: Verify the S3 bucket/prefix is correct and that the configured IAM user/role has list and read permissions.
- **Slow or incomplete sync**: Narrow the S3 prefix, reduce filters, or allow more time for the initial indexing to complete before downstream steps depend on the data.
- **Access denied errors**: Confirm bucket policies, ACLs, and IAM policies allow the Carbon connector to read the specified resources.
