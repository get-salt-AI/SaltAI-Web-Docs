# S3

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector targeting Amazon S3. It delegates to the Carbon integration layer using the S3 integration type and inherits all behavior from the Carbon data connector base class.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectors3.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to register or reference an S3 data source within Carbon-powered workflows (e.g., syncing or accessing files stored in an S3 bucket). Typically placed early in a data pipeline to define the S3 source before subsequent processing or synchronization nodes.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the shared Carbon data connector base node. Specific fields for S3 (e.g., bucket configuration or credentials) are not specified in the exposed implementation.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the shared Carbon data connector base node. Typically this would provide a configured connector handle or reference for downstream nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is a thin specialization that selects the S3 integration; all configuration, input/output schema, and execution behavior come from the Carbon data connector base node.
- Display name is "S3" in the UI.
- Ensure your environment or platform has appropriate credentials and access to S3 and to the Carbon service as required.

## Troubleshooting
- If the node appears but provides no fields, verify the Carbon base connector node is available and correctly loaded in your environment.
- If connections to S3 fail at runtime, check that S3 credentials, bucket permissions, and network access are correctly configured in the platform or Carbon service configuration.
- If downstream nodes cannot use this connector, ensure they accept and are wired to the connector output type defined by the Carbon base node.
