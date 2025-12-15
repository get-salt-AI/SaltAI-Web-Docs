# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects one or more node outputs and uploads them to Salt-managed cloud storage under a specified folder path. The node organizes files by their source and metadata, merges results for the same folder across runs using a unique workflow execution identifier, and returns a signed URL to a zip archive of the uploaded content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/savetobucketnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to persist results to a cloud bucket. Connect any node outputs (text, JSON, or file-like data with metadata) to the sequential results inputs. Specify a non-empty folder_path to determine where the files are stored in the bucket. Finally, route the produced URL to an Output node to make it clickable for download.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First result to save. Once connected, results_2 becomes available. Accepts any output type; file-like objects with salt metadata will be saved using their provided names and structure.</td><td style="word-wrap: break-word;">Any connected output (e.g., a PDB object with salt metadata, or a string/JSON)</td></tr>
<tr><td style="word-wrap: break-word;">results_2 ... results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional results to save. Each subsequent input is revealed after the previous one is connected, allowing up to 101 total results.</td><td style="word-wrap: break-word;">Additional outputs from other nodes</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Destination folder path within the cloud bucket. This folder may contain results from other runs; new results are merged with a unique execution identifier to differentiate runs. Supports nested paths using Linux-style separators.</td><td style="word-wrap: break-word;">biotech/projectA/experiments/run-set-1</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL to a zip archive representing the current content saved to the specified bucket folder for this execution.</td><td style="word-wrap: break-word;">https://storage.example/signed/<path>/<file>.zip</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Folder path required**: folder_path must be non-empty; saving to the root is not allowed.
- **Sequential inputs**: results_2 becomes available only after results_1 is connected, and so on, up to results_101.
- **Merging behavior**: Files are organized by metadata and source; results for the same folder_path are merged and separated by workflow_execution_id.
- **File organization**: Inputs with salt metadata (e.g., PDB, A3M) are saved using their provided file names and sub-structure; plain values are saved as text files named by their source and output index.
- **Signed URL**: The returned link is a signed URL intended for download; it may be time-limited.
- **Cleanup**: Temporary local data used during upload is cleaned up after completion.

## Troubleshooting
- **ValueError: Please specify non empty folder path**: Provide a non-empty folder_path (avoid '/' or empty string).
- **No output URL produced**: Ensure at least results_1 is connected and the workflow executed successfully.
- **Unexpected file naming or folders**: Provide proper salt metadata on file-like inputs to control file names and structure; otherwise files are saved using default naming based on source node and output index.
- **Signed URL expired**: Re-run the node to generate a fresh URL.
- **Large uploads timing out**: If very large result sets fail due to time limits, split outputs across multiple Save To Bucket nodes or reduce the data per run.
- **Permissions/user issues**: Make sure salt_user_id is valid for your workspace. Contact your administrator if uploads are blocked.

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

