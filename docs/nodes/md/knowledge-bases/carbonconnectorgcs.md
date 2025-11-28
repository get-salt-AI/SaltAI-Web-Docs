# Google Cloud Storage

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and configures the Google Cloud Storage integration for Carbon data workflows. This node identifies the data source as GCS and delegates all core behavior (inputs, processing, and outputs) to the shared Carbon data node base. It is typically used to point downstream Carbon data operations at a GCS-backed source.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorgcs.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a Carbon data ingestion or synchronization flow when your source is Google Cloud Storage. Place it before nodes that define scope, scheduling, or downstream processing so they know which integration to use. Ensure the associated Carbon integration and permissions are configured for your user or workspace.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the shared Carbon data node base. This connector only selects the GCS integration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the shared Carbon data node base. This connector only selects the GCS integration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node only selects the Google Cloud Storage integration; all functional behavior (inputs/outputs/processing) comes from the base Carbon data node.
- You must have a valid Carbon integration and appropriate permissions to access the target GCS resources (e.g., buckets/paths).
- Do not paste secrets or keys into node fields; use secure connection mechanisms and environment-managed credentials.
- Downstream nodes will use the selected integration (GCS) for listing, syncing, or processing content.

## Troubleshooting
- Authentication or authorization error: Verify your Carbon account connection to GCS and that your user/workspace has access to the required buckets and paths.
- Cannot see expected files or buckets: Check the configured project/bucket access policies and ensure the service principal or linked credentials have the necessary roles.
- Unexpected behavior in inputs/outputs: Confirm the required parameters for the base Carbon data node are provided, as this connector inherits those requirements unchanged.
