# Documents

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

CarbonConnectorLocalFiles connects to a local filesystem source through the Carbon LOCAL_FILES integration. It leverages the shared Carbon data node behavior to discover, list, or fetch documents that reside on disks or mounted volumes accessible to the running Salt services.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/knowledge-bases/carbonconnectorlocalfiles.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your documents live on the same machine (or mounted storage) as your Salt stack and you want to surface them into your workflows. It is typically placed at the start of a knowledge or retrieval pipeline, feeding downstream nodes that parse, chunk, embed, or otherwise process documents.

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
<tr><td style="word-wrap: break-word;">Not specified</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">This node inherits its inputs from a shared Carbon data node base. Specific input fields (such as path, filters, or limits) are defined by the base behavior and UI. Configure them in the node’s panel to target the desired local files.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">documents</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A dataset or reference representing the documents discovered/fetched from the local filesystem, suitable for downstream processing (e.g., parsing, splitting, embedding).</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **LOCAL_FILES integration**: This node is bound to the Carbon LOCAL_FILES integration and automatically selects that source; you do not need to choose the integration manually.
- **Server-side paths**: Paths and files must be accessible to the server environment where Salt services run (e.g., inside a container or VM). Local paths on your personal machine will not work unless mounted and exposed to the service.
- **Permissions**: Ensure the service account running Salt has read permissions for the targeted directories and files.
- **Behavior is inherited**: Inputs, output structure, and filtering logic come from the base Carbon data node. The exact fields and behavior may vary depending on platform configuration.

## Troubleshooting
- **No files returned**: Verify the specified path exists in the server environment and is mounted into the service. Confirm read permissions and adjust any filters (extensions, recursion, or limits) to broaden the search.
- **Permission errors**: Ensure the service user has read access to the directories and files. Update filesystem permissions or mount options accordingly.
- **Unexpected file set**: Check filters (such as file patterns, size limits, or modified date) in the node’s configuration and confirm they match your expectations.
- **Path works locally but not in Salt**: Remember that paths must be valid inside the service/container runtime. Mount or map local directories into the environment where the node executes.
