# Documents

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Carbon data connector configured for Local Files. It leverages the common Carbon data-connector base to reference the LOCAL_FILES integration, enabling workflows to access and synchronize documents from a local filesystem.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorlocalfiles.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to bring documents from a local folder into a Carbon-powered pipeline (e.g., indexing, syncing, or processing local files). Typically, you place this node at the start of a data ingestion flow and connect it to downstream nodes that perform parsing, enrichment, or indexing.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Inputs are defined by the shared Carbon data-connector base. This node selects the Local Files integration; any configuration fields (e.g., path, include/exclude patterns, recursion settings) are provided by the base node.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Outputs are defined by the shared Carbon data-connector base. Typically this will be a connector/config object used by downstream Carbon data ingestion or indexing nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Integration selection**: This node specifically targets the Local Files integration within Carbon.
- **Shared behavior**: All input fields, validation, and output structure come from the Carbon data-connector base; this node only specifies the integration type.
- **Access and permissions**: Ensure the runtime environment has read permissions to the specified local paths.
- **Service dependency**: Requires the Carbon service and user authorization to be properly configured in your environment.
- **Display name**: Appears as “Documents” in the node picker.

## Troubleshooting
- **No files found**: Verify the configured folder path exists and is accessible by the runtime user.
- **Permission errors**: Ensure the process has read permissions on the target directories and files.
- **Authorization failures**: Confirm your Carbon service credentials/token are valid and the service is reachable.
- **Unexpected output**: Check the downstream nodes’ expected input type; this node provides a Carbon connector reference, not parsed file content.
- **Long scan times**: Narrow the scope with include/exclude patterns if available in the base configuration to reduce traversal time.
