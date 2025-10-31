# S3

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Provides a Carbon data integration connector for Amazon S3. It configures the node to use the S3 integration within the Carbon platform so downstream nodes can access or operate on data sourced from S3.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectors3.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to connect workflows to Amazon S3 via the Carbon integration layer. Typically, place it early in a data pipeline to establish the S3 data source, then pass its output to nodes that query, list, or process files/objects managed by Carbon.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The node inherits its interface from a shared Carbon data node base. Specific input fields (e.g., authentication, workspace, bucket, prefix) are not specified here.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs whatever the base Carbon data node defines for a configured S3 integration (e.g., a handle or reference used by downstream Carbon-aware nodes).</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node selects the Amazon S3 integration for the Carbon data layer; ensure your Carbon environment has S3 properly configured.
- Access and permissions to S3 are managed through Carbon; make sure the underlying credentials and IAM permissions are in place.
- This node’s exact inputs/outputs are defined by the shared Carbon data node base and may vary by platform version.

## Troubleshooting
- If you see authorization or permission errors, verify the Carbon S3 integration credentials and IAM policies.
- If downstream nodes cannot read data, confirm the target bucket/prefix exists and is accessible in the configured Carbon environment.
- If the node doesn’t appear or fails to initialize, ensure the Carbon services are reachable and that the S3 integration is enabled in your environment.
