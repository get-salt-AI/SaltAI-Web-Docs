# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects outputs from upstream nodes, organizes them into a structured folder layout, and uploads the result set to a cloud bucket location. Returns a signed URL to a zip that represents the uploaded folder contents, suitable for downloading the full run output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/savetobucketnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow to persist results to a cloud bucket. Connect one or more outputs (text, structured data, or domain-specific types) to results_1, results_2, etc. Provide a destination folder path within the bucket. The node will group files by producing node and metadata when available, generate seeds/configs manifests if provided by inputs, upload everything, and return a signed URL for convenient download.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First result to save. Accepts any connected output. Additional inputs results_2..results_101 become available as you connect more results.</td><td style="word-wrap: break-word;">Dictionary of PDBs, a FASTA string, or any text-like output</td></tr>
<tr><td style="word-wrap: break-word;">results_2..results_101</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional results to save. These appear progressively after connecting the previous result input.</td><td style="word-wrap: break-word;">Additional outputs such as JSON-like configs, A3M dictionaries, etc.</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the destination folder in the cloud bucket. Results may be merged with other runs by a unique workflow execution identifier. Supports nested paths using forward slashes.</td><td style="word-wrap: break-word;">biotech/experiments/run_outputs</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL to a zip representing the current content of the uploaded folder. Use this to download the results.</td><td style="word-wrap: break-word;">https://storage.example.com/path/to/results.zip?X-Goog-Signature=<signature></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Folder path is required**: The node rejects empty folder paths and normalizes leading/trailing slashes.
- **Input organization**: Files are grouped into folders based on upstream metadata when available (including per-input folder/file names); otherwise they are grouped by producing node and output index.
- **Manifests**: If upstream metadata includes seeds and/or configuration values, seeds.json and configs.json are added at the root of the uploaded folder.
- **Content handling**: Inputs are saved as text files. Complex objects are stringified, and certain domain types (e.g., PDB, A3M) are written as individual files per item.
- **Multiple results**: You can chain up to 101 results inputs (results_1 through results_101). Inputs appear progressively as you connect the previous one.
- **Signed link validity**: The returned URL is a signed link. Its availability and expiration are managed by the platform configuration.
- **Execution namespacing**: Results are associated with the current workflow execution identifier to distinguish multiple runs in the same destination folder.

## Troubleshooting
- **Error: Please specify non empty folder path**: Provide a non-empty folder_path. Do not rely on the bucket root.
- **Upload timeout or no URL returned**: Ensure the platform’s eventing and storage services are available and you have permission to write to the bucket. Retry the run.
- **Unexpected folder structure**: Some inputs may lack metadata. In that case, files are grouped by producing node class and output index. Add or propagate metadata upstream if you need specific names.
- **Binary or large data not saved as expected**: The node writes results as text. Ensure inputs are text-based or converted to a text representation before saving.
- **Signed URL not downloadable**: The link may have expired or been revoked. Re-run the node to generate a fresh URL or check platform policies.

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

