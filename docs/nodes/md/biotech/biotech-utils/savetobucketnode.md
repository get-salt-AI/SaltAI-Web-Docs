# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects connected results, organizes them into a well-structured folder (including optional seeds/configs), uploads the folder’s content to a specified path in a cloud bucket, and returns a signed URL to a downloadable zip of that content. Each run is uniquely namespaced to avoid collisions and to allow merging results from multiple runs under the same folder path.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/savetobucketnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to persist and share results externally. Connect one or more outputs (results_1, results_2, …) from upstream nodes. Set the target bucket folder path to organize outputs. The node will package all connected outputs, upload them to the bucket, and provide a signed URL you can pass to an Output node for easy downloading.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First result to save. Accepts any connected output (text, JSON-like, structured text formats). Enables results_2 once connected.</td><td style="word-wrap: break-word;">{'summary': 'job completed', 'metrics': {'loss': 0.12}}</td></tr>
<tr><td style="word-wrap: break-word;">results_2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional result to save. Appears after results_1 is connected. Up to 100 results are supported (results_2 ... results_101).</td><td style="word-wrap: break-word;">FASTA or PDB text content</td></tr>
<tr><td style="word-wrap: break-word;">results_3..results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Optional additional results. Each becomes available after the previous result input is connected.</td><td style="word-wrap: break-word;">Additional text outputs, structured text files, or logs</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination folder path inside the cloud bucket. Results from multiple runs can be merged here; each run is uniquely identified to avoid overwriting. Supports nested paths using forward slashes (e.g., 'projectA/experiments/run1').</td><td style="word-wrap: break-word;">biotech/my_experiment_1</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL to a zip file containing all uploaded results for this node’s run within the specified bucket folder.</td><td style="word-wrap: break-word;">https://storage.googleapis.com/<bucket>/biotech/my_experiment_1/<unique_run_id>/<unique_run_id>.zip?X-Goog-Signature=<signature></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Folder path must not be empty; the node will raise an error if it is blank.
- Results are grouped and written into a logical folder structure; multiple runs within the same folder_path are uniquely namespaced to avoid conflicts.
- This node returns a signed URL to a zip representing the uploaded folder’s contents, suitable for direct download via an Output node.
- The node is intended as a terminal step in workflows to persist and share outputs externally.
- If user scoping is required, ensure a valid salt_user_id is available in the execution context.

## Troubleshooting
- Error: 'Please specify non empty folder path' — Provide a non-empty folder_path (avoid using the root of the bucket).
- No URL returned or upload appears incomplete — Verify that connected results contain data and that the execution did not timeout. Re-run with fewer/lighter outputs if necessary.
- Access issues when opening the signed URL — Ensure the URL has not expired and that you copied the full link. Re-run to generate a fresh URL if needed.
- Unexpected file/folder naming — The node auto-resolves name collisions by appending indexes; review the resulting zip structure to locate files.
- Missing expected result inputs — Remember that results_2 becomes available only after results_1 is connected, and so on for subsequent inputs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetobucketnode/directory-structure.png" alt="Directory structure" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Directory structure</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetobucketnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/savetobucketnode/merged-table.png" alt="Merged table" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Merged table</figcaption>
</figure>

