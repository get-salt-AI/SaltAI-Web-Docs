# Save To Zip

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Aggregates outputs from multiple upstream nodes, organizes them into a structured folder layout, and creates a zip archive. The node intelligently names folders/files (with collision-avoidance) and, when available, uses metadata from inputs to preserve meaningful filenames and group related results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/savetozipnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow when you want to package results into a single downloadable zip. Connect any outputs (e.g., sequences, PDB/A3M dictionaries, text, or other data) to results_1 and subsequent results_N inputs. The node will produce a URL/path to the created zip, which you can connect to the Output node to download.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First input to include in the zip. Accepts any output from any node. Once connected, results_2 becomes available.</td><td style="word-wrap: break-word;">Any node output, e.g., a dictionary of PDBs, an A3M dict, or a string.</td></tr>
<tr><td style="word-wrap: break-word;">results_2 ... results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional inputs to include in the zip. Each appears after connecting the previous one, allowing up to 100 additional inputs.</td><td style="word-wrap: break-word;">Any node output, e.g., additional PDB/A3M dicts, FASTA strings, or other text values.</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">URL or path to the folder containing the created zip archive for this workflow run. Connect to the Output node to click and download.</td><td style="word-wrap: break-word;">https://download/<workflow_execution_id>_<index>/<workflow_execution_id>_<index>.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequential input reveal**: results_2 only appears after connecting results_1, results_3 after results_2, and so on (up to results_101).
- **Metadata-aware saving**: Inputs produced by nodes that embed metadata (e.g., file_name, folder_name) are saved using those names, with automatic numbering to avoid clashes.
- **Structured handling for PDB/A3M**: When inputs are PDB or A3M dictionaries, each entry is saved as a separate file within a subfolder.
- **Non-metadata inputs**: Values without metadata are saved as text files (e.g., output_<n>.txt) in a folder named after their source node class.
- **Seeds and configs capture**: If provided via input metadata, consolidated seeds.json and configs.json are added at the top level of the zip content.
- **Collision handling**: If a file/folder name already exists, a numeric suffix is appended to avoid overwriting.
- **Execution priority**: Designed to run at the end of a workflow to capture all upstream outputs.
- **Output location**: The node returns a URL/path where the zip can be downloaded; connect it to the Output node to expose a clickable link.

## Troubleshooting
- **Cannot see results_2 or further**: Connect results_1 first; additional inputs are revealed only after the previous one is connected.
- **Download link not clickable**: Ensure the url output is connected to an Output node to render a clickable link.
- **Unexpected file names or folders**: If metadata is missing on an input, the node falls back to generic names (e.g., source node class and output_<n>.txt). To influence naming, use nodes that provide metadata-aware outputs.
- **Missing files in zip**: Verify that each desired upstream output is connected to a results_N input and that the inputs are not None.
- **Permission or path issues**: If the generated path cannot be accessed or saved, check environment permissions and the configured output directory.
- **Large inputs or many files**: Packaging many or large outputs may take longer; allow the node to complete before attempting to download.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetozipnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetozipnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

