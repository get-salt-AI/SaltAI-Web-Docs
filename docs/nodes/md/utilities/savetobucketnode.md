# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects one or more upstream results, organizes them into a structured folder, uploads the folder contents to a specified path in a cloud bucket, and returns a signed URL to a downloadable zip of that content. Each run is uniquely namespaced to avoid overwriting existing data while still allowing multiple runs to be merged under the same bucket folder path.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/savetobucketnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow when you need to persist outputs externally and share them as a downloadable archive. Connect one or more upstream outputs to results_1, results_2, and so on, then set folder_path to the desired destination path in your cloud bucket. The node will package all connected results, upload them into a run-specific subfolder under folder_path, and emit a signed URL that you can forward to an Output node or external system for downloading the zip file.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary result to save. Accepts any connected output, such as text, JSON-like structured data, logs, or domain-specific text formats. Once this is connected, the next result input (results_2) becomes available.</td><td style="word-wrap: break-word;">{ "summary": "job completed", "metrics": { "loss": 0.12 } }</td></tr>
<tr><td style="word-wrap: break-word;">results_2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Optional additional result to save. Becomes available after results_1 is connected. Can hold any similar output type (e.g., text, structured text, sequence data).</td><td style="word-wrap: break-word;">FASTA or PDB text content</td></tr>
<tr><td style="word-wrap: break-word;">results_3..results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Further optional result inputs for saving multiple outputs in the same run. Each input appears only after the previous one is connected, up to a total of 100 results (results_1 through results_101).</td><td style="word-wrap: break-word;">Additional text outputs, structured text files, reports, or logs</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination folder path inside the cloud bucket where this run’s data will be stored. Multiple runs can use the same folder_path; each run gets a unique subfolder to prevent overwrites. Supports nested paths using forward slashes.</td><td style="word-wrap: break-word;">biotech/my_experiment_1</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL pointing to a zip file that contains all uploaded results for this node’s run under the specified bucket folder. Suitable for direct download or consumption by downstream nodes.</td><td style="word-wrap: break-word;">https://storage.googleapis.com/<bucket>/biotech/my_experiment_1/<unique_run_id>/<unique_run_id>.zip?X-Goog-Signature=<signature></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Folder path must be set**: The folder_path input cannot be empty; a blank value will cause the node to raise an error.
- **Unique run namespace**: Each execution is written into a unique subfolder under folder_path so that multiple runs do not overwrite each other and can safely share the same logical folder.
- **Packaged as a zip**: The node organizes all connected results into a directory structure and returns a signed URL to a single zip archive of that structure.
- **End-of-workflow usage**: This node is intended to be used as a final or near-final step to persist and share results outside of SaltAI.
- **User scoping considerations**: If you require per-user isolation, ensure a valid salt_user_id is present in the execution context so that storage paths or access controls are correctly scoped.
- **Dynamic result inputs**: Inputs results_2 through results_101 appear only after the previous result input is connected, enabling you to incrementally add more results.

## Troubleshooting
- **Error: "Please specify non empty folder path"**: Provide a non-empty folder_path and avoid using the absolute root of the bucket as the target path.
- **No URL returned or archive seems incomplete**: Confirm that upstream nodes produced data and that the workflow run did not time out. If the data volume is large, try rerunning with fewer or smaller outputs.
- **Cannot open signed URL**: Verify that the full URL including query parameters was copied and that it has not expired. Re-run the node to generate a fresh URL if necessary.
- **Unexpected file or folder names in the zip**: The node may adjust names or append indices to avoid collisions. Inspect the folder hierarchy inside the zip to locate your files.
- **Additional result inputs not visible**: Remember that results_2 only appears after results_1 is connected, and each subsequent results_N appears only after the prior one is wired. Connect earlier inputs to reveal more.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetobucketnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetobucketnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/savetobucketnode/merged-table.png" alt="Merged table" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Merged table</figcaption>
</figure>

