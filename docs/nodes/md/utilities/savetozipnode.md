# Save To Zip

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Aggregates outputs from multiple upstream nodes, organizes them into a structured folder layout, and packages everything into a zip archive. It uses available metadata to preserve meaningful filenames and folders and automatically avoids name collisions. The node returns a URL or path to the generated zip so it can be downloaded.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/savetozipnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow when you want to collect and download all relevant results as a single zip file. Connect the first desired output to results_1; once connected, additional results_N inputs (up to results_101) become available for more outputs. Finally, connect the url output to an Output node so that the generated zip link is exposed for download.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary input to include in the zip. Accepts any output from any node, such as structured result dictionaries, text, sequences, or other data types. Connecting this input reveals results_2.</td><td style="word-wrap: break-word;">A dictionary where keys are identifiers and values are PDB file contents or A3M alignments.</td></tr>
<tr><td style="word-wrap: break-word;">results_2 ... results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional optional inputs to include in the zip. Each appears only after the previous results_N input is connected, allowing you to flexibly add up to 100 more inputs.</td><td style="word-wrap: break-word;">Additional PDB or A3M dictionaries, FASTA strings, logs, or other node outputs to be bundled into the same zip.</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">URL or filesystem path to the folder containing the created zip archive for this workflow run. Connect this to an Output node to make the link clickable and allow users to download the zip.</td><td style="word-wrap: break-word;">https://download/<workflow_execution_id>_<index>/<workflow_execution_id>_<index>.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequential input reveal**: Only results_1 is visible initially; results_2 appears after results_1 is connected, results_3 after results_2, and so on up to results_101.
- **Metadata-aware naming**: When upstream nodes provide metadata such as file_name or folder_name, the zip uses those to create human-readable file and folder names, adding numeric suffixes when needed to avoid collisions.
- **Structured handling for PDB/A3M**: Inputs that are PDB or A3M dictionaries are split into separate files, typically grouped into subfolders so each entry becomes an individual file inside the zip.
- **Fallback naming for plain values**: Inputs without metadata are saved as text files (for example, output_1.txt or output_2.txt) inside a folder named after the source node class.
- **Config and seed capture**: When available in metadata, consolidated configs.json and seeds.json files are placed at the top level of the zip to document how the results were generated.
- **Designed as a terminal step**: This node is intended to run at the end of a workflow, after all desired results are produced, so the zip includes the complete set of outputs.
- **Collision handling**: If two files or folders would have the same name, the node appends a numeric suffix to create unique names without overwriting existing content.
- **Download exposure**: The node only returns a path or URL; to make it usable in the UI, its url output must be wired into an Output node.

## Troubleshooting
- **Cannot see results_2 or higher inputs**: The additional inputs appear only after the previous one is connected. Start by wiring something into results_1; then results_2 will become available, and so on.
- **Download link is not clickable**: Make sure the url output of Save To Zip is connected to an Output node so the platform can render a clickable link.
- **Unexpected file or folder names in the zip**: If upstream nodes do not provide metadata, the node uses generic names such as the source node class and output_n.txt. To control naming, use nodes that emit metadata like file_name or folder_name along with their outputs.
- **Some expected results are missing from the zip**: Confirm that each desired output is actually connected to a results_N input and that none of those inputs are None or disconnected.
- **Errors or issues accessing the generated path**: Check file system permissions and any configured output directory location on the environment where the workflow runs.
- **Slow performance or long wait times**: Creating zips from many or large outputs can take noticeable time. Allow the node to finish processing before attempting to download; consider reducing the number or size of inputs if performance is critical.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetozipnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetozipnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

