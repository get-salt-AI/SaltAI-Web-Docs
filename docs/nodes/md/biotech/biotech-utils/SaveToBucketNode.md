# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Saves connected node outputs into a structured folder in a cloud bucket and returns a signed URL to a zip snapshot of that folder. It organizes files by source node and any attached metadata (folder/file names), supports many inputs, and merges results using the workflow execution ID to distinguish runs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/SaveToBucketNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to persist all relevant results to a cloud bucket and obtain a downloadable link. Connect any outputs (texts, structured strings, dictionaries like PDB/A3M, etc.) to results_N. Provide a non-empty folder_path to control where the data is stored in the bucket. The node uploads all files, merges them under a unique execution ID, and returns a signed URL to download a zip of the saved contents.

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
<tr><td style="word-wrap: break-word;">results_N (N = 1..101)</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Connect any output you want to save (strings, dict-like biotech formats, etc.). Inputs with Salt metadata will be written using their provided folder/file names; others will be stored under a folder named after the source node. For dict-like biotech inputs (e.g., PDB/A3M), items are split into separate files.</td><td style="word-wrap: break-word;">A PDB dict like {"protein1": "PDB_CONTENT..."} or a STRING such as "inference complete"</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination path inside the cloud bucket. Must be non-empty and should not start or end with '/'. Nested paths are supported using forward slashes.</td><td style="word-wrap: break-word;">biotech/runs/alphafold_experiment</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL to a zip file that represents the current content of the destination folder in the bucket. Use it to download the saved results.</td><td style="word-wrap: break-word;">https://storage.signed-url.example/download?sig=...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Folder path must be non-empty and should not include leading or trailing slashes; nested folders are allowed (e.g., project/experiment1).
- If multiple inputs result in the same file name, the node will auto-suffix with _2, _3, etc., to avoid overwriting.
- Inputs carrying Salt metadata control their folder/file names and may also add seeds.json and configs.json at the folder level.
- Dictionary inputs for biotech formats (e.g., PDB, A3M) are saved as multiple files, one per entry.
- This node merges results by workflow execution ID so multiple runs under the same folder_path can coexist and be distinguished.

## Troubleshooting
- Error: 'Please specify non empty folder path.' — Set a valid non-empty folder_path without leading/trailing slashes.
- No download link returned — Ensure at least one results_N input is connected and that the workflow completed without errors.
- Timeout while uploading — Large numbers of files or very large content may exceed the operation window. Reduce output size or try splitting saves.
- Unexpected file naming or structure — Check whether inputs provided Salt metadata; metadata dictates directory and file names. Without metadata, files are placed in a folder named after the source node.
- Missing expected files for dict-like inputs — Verify the input format. For PDB/A3M, ensure the input is a dictionary of name->content.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/SaveToBucketNode/Directory structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/SaveToBucketNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/SaveToBucketNode/Merged table.png" alt="Merged table" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Merged table</figcaption>
</figure>

