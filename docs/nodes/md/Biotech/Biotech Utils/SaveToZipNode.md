# Save To Zip

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects one or more connected node outputs, organizes them into a structured temporary folder, and bundles everything into a single zip archive. It auto-names folders/files based on the source nodes and any provided metadata, and returns a link to access the generated archive.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Biotech Utils/SaveToZipNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to export results as a single downloadable archive. Connect any node outputs to results_1, results_2, etc. Once connected, the next results_N input becomes available, allowing you to aggregate many outputs. Then connect the produced url to an Output node to click and download the archive.

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
<tr><td style="word-wrap: break-word;">results_1...results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Accepts any output from any node. Each connected input is saved into the archive. The next results_N input becomes visible after connecting the previous one.</td><td style="word-wrap: break-word;">Any data type (e.g., strings, dicts, model outputs)</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A link to the created archive location. Connect this to an Output node to click and download locally.</td><td style="word-wrap: break-word;">https://server/output/WORKFLOWID_0/WORKFLOWID_0.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Dynamic inputs**: results_1 is required; results_2 through results_101 appear progressively as you connect the previous one.
- **Archiving behavior**: All connected inputs are written into a folder structure and zipped. JSON-like content is saved as formatted JSON when possible; other types are stringified.
- **Metadata-aware saving**: If an input carries Salt metadata (folder_name, file_name, seed, config), it will be placed accordingly, and seeds.json/configs.json may be added per folder.
- **Batch dictionaries**: For certain structured inputs like PDB or A3M dictionaries (when metadata indicates), items are split into per-entry files inside a subfolder.
- **Organization for non-metadata inputs**: Inputs without metadata are grouped by their source node’s class into uniquely named folders; file names follow output_<index>.txt.
- **Execution priority**: This node acts as an output node to finalize and produce the archive.
- **Unique naming per run**: The archive is uniquely identified using workflow_execution_id and the node’s position in the workflow.
- **Download tip**: The url should be connected to an Output node for clickable download access.

## Troubleshooting
- **No download link appears**: Ensure you connected the url output of this node to an Output node to expose a clickable link.
- **Missing files in archive**: Verify the expected inputs are connected to results_N. Only connected inputs are included.
- **Unexpected folder/file names**: If inputs lack metadata, the node names folders based on the source node’s class and output index; add metadata-aware nodes upstream if you need precise names.
- **Incorrect content format**: Non-JSON data is saved as text via string conversion. If you require structured JSON, ensure upstream nodes provide JSON-serializable data.
- **Archive collision concerns**: workflow_execution_id ensures uniqueness. If multiple Save To Zip nodes exist, each gets a unique index; confirm the correct link is used.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Biotech Utils/SaveToZipNode/Directory structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Biotech Utils/SaveToZipNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

