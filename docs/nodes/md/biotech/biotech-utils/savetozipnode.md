# Save To Zip

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects and organizes multiple node outputs into a structured folder and creates a ZIP archive for download. Inputs can be connected progressively (results_1, results_2, ...) and are grouped into folders based on metadata or source node, with optional seeds/configs included.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/savetozipnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to bundle outputs from various nodes into a single downloadable ZIP. Connect any output to results_1, then chain additional outputs to results_2, results_3, etc. The node will automatically structure files into folders, include seeds/configs if provided by upstream nodes, and generate a unique archive per workflow execution.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First output to include in the ZIP. Accepts any output from any node. Enables the next input when connected.</td><td style="word-wrap: break-word;">Any data type, e.g., a string, number, dict of files like {'model.pdb': '...'}, or other node-specific outputs.</td></tr>
<tr><td style="word-wrap: break-word;">results_2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second output to include in the ZIP. Appears only after results_1 is connected.</td><td style="word-wrap: break-word;">Any additional output to save.</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or URL to the output location containing the generated ZIP archive for this workflow execution.</td><td style="word-wrap: break-word;">/path/to/output/<workflow_execution_id>_<index>/<workflow_execution_id>_<index>.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Connect outputs in order: results_1 must be connected before results_2 becomes available, and so on up to results_101.
- Inputs that carry Salt metadata (folder_name, file_name, seed, config) are saved using that structure; seeds and configs (if present) are aggregated into seeds.json and configs.json at the root.
- If an input contains multiple sub-files (e.g., PDB or A3M collections), the node writes them as separate files under a folder matching the main file name.
- Inputs without metadata are placed into a folder named after the source node class, with file names like output_<index>.txt.
- The generated archive is uniquely named using the workflow_execution_id (and the index of this Save node instance) to avoid collisions.
- The output location is environment-dependent; the runtime may expose a downloadable link in the Output node.

## Troubleshooting
- No files in ZIP or missing expected files: Ensure each results_i is connected and producing a value at execution time.
- Unexpected folder or file naming: Check if upstream nodes provide Salt metadata (folder_name, file_name). Without metadata, generic names are used.
- Download link not clickable or missing: Connect the 'url' output to an Output node or the designated display node for links.
- Permission or write errors: Verify the runtime has write access to the configured output directory.
- Invalid or complex data structures not saved as JSON: Non-serializable data is saved as plain text; consider converting outputs to strings or simple JSON-serializable objects upstream.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetozipnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetozipnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

