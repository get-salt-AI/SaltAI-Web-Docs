# Save To Bucket

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaveToBucketNode collects multiple upstream node outputs, writes them into a structured temporary directory using metadata-based folder and file naming, and uploads the resulting content to a Google Cloud Storage bucket via the Salt biotech service. It uses workflow and user context to generate a unique execution identifier, allowing multiple runs to coexist under the same logical bucket folder without collisions. The node returns a signed URL pointing to an archive of the uploaded folder so results can be downloaded or consumed by downstream systems.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/savetobucketnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaveToBucketNode when you need to persist workflow outputs to cloud storage instead of (or in addition to) local disk. Common scenarios include archiving generated protein structures (PDB/CIF), MSAs (A3M), FASTA sequences, logs, and configuration files from biotech pipelines, and then sharing them with collaborators or other systems.

Place this node near the end of your workflow as a sink node: connect outputs from your computation, visualization, or data-preparation nodes into its `results_*` inputs. The node collects all connected values, organizes them into folders and files (leveraging `salt_metadata` when present), and then uploads the directory to a GCP bucket using the `salt-saas-biotech-cpu-upload-results-to-gcp` service topic. Set `folder_path` to a meaningful bucket subfolder (for example grouped by project or experiment); the node appends a unique execution identifier so multiple runs and multiple SaveToBucketNode instances do not overwrite each other.

This node is especially useful in workflows that already use biotech nodes like LoadPDBNode, PDBVisualizationNode, PDBToFastaNode, PDBCombinerNode, or PdbFixerNode, which attach metadata used by SaveToBucketNode to produce clean, well-organized output structures. Downstream, connect the `url` output to an Output node for manual download, or to custom HTTP/notification nodes to distribute the link automatically.

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
<tr><td style="word-wrap: break-word;">results_1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First result to save. Accepts any upstream output (e.g., PDB/CIF dictionaries, MSA dictionaries, FASTA strings, plain strings or numbers). If the value has `salt_metadata` (with `folder_name`, `file_name`, `seed`, and `config`), those fields determine where and how the file is written; otherwise the value is serialized as text into a generic file in a node-derived folder.</td><td style="word-wrap: break-word;">{'value': {'protA_model1': 'ATOM      1  N   MET A   1 ...\\nATOM      2  CA  MET A   1 ...\\nEND'}, 'salt_metadata': {'folder_name': 'alphafold_run', 'file_name': 'structure.pdb', 'seed': 42, 'config': {'model': 'AlphaFold2-multimer', 'num_ensembles': 1}}}</td></tr>
<tr><td style="word-wrap: break-word;">folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical path to the output folder in the GCP bucket, in Unix-style notation without leading or trailing slashes. Must be non-empty. The folder may already contain results from other runs; new uploads are associated with their own execution identifier under this path so they do not overwrite prior runs.</td><td style="word-wrap: break-word;">biotech/alphafold/experiment_42</td></tr>
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
<tr><td style="word-wrap: break-word;">url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Signed URL returned by the biotech upload service. It points to an archive representing the uploaded folder contents for this execution under the specified bucket path. This URL can be wired into an Output node for easy download or consumed by automation that retrieves and processes the results.</td><td style="word-wrap: break-word;">https://storage.googleapis.com/salt-bucket/biotech/alphafold/experiment_42/results_2024-06-11_7f3c21.zip?X-Goog-Algorithm=GOOG4-RSA-SHA256&X-Goog-Credential=<gcp-credential>&X-Goog-Date=20240611T093215Z&X-Goog-Expires=3600&X-Goog-Signature=<signature></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: All connected inputs are first written to a temporary local directory and then uploaded; large batches of big PDB/CIF/A3M files can increase disk, memory, and network usage and may approach the 600-second upload timeout for the biotech service.
- **Limitations**: `folder_path` cannot be empty or become empty after trimming slashes. The node will raise a ValueError instead of writing to the bucket root, so always provide a meaningful subpath like `projectA/run_001`.
- **Behavior**: Values with `salt_metadata` are stored in folders named by `folder_name` and files named by `file_name`; when the content is a dictionary of structures, each entry becomes its own file. Seeds and configs from metadata are aggregated into `seeds.json` and `configs.json` at the root of the run directory.
- **Behavior**: Values without metadata are grouped by their producing node class into folders, and are written as text files named `output_<outputIndex>.txt`. Folder-name collisions are automatically avoided by adding numeric suffixes when needed.
- **Behavior**: The node constructs a unique execution identifier from `workflow_execution_id` and its position in the workflow, allowing multiple SaveToBucketNode instances and multiple executions to reuse the same `folder_path` without overwriting each other.
- **Behavior**: After attempting the upload (successfully or not), the temporary source directory is always removed, so this node does not provide any durable local storage of the generated files.

## Troubleshooting
- **Error: 'Please specify non empty folder path. Saving files to the root folder is not recommended.'**: This occurs if `folder_path` is empty or only slashes after trimming. Set `folder_path` to a non-empty relative path such as `projectA/run_001` and avoid leading or trailing `/`.
- **Symptom: Upload hangs and then fails due to timeout**: Very large result sets or slow network/GCP performance can exceed the 600-second timeout of the biotech upload event. Reduce the size or number of files per run, or split your pipeline into multiple SaveToBucketNode endpoints to keep each upload smaller.
- **Symptom: Expected files are missing or have generic names**: When upstream outputs lack `salt_metadata`, SaveToBucketNode falls back to node-class-based folders and generic `output_<index>.txt` filenames. To get predictable paths and filenames, ensure biotech nodes that produce important outputs attach metadata, and double-check which outputs you connect to `results_*`.
- **Error or failure from the biotech upload event (permissions/auth)**: If the underlying `salt-saas-biotech-cpu-upload-results-to-gcp` call fails with an authorization or connectivity error, verify that your environment has valid GCP credentials configured, that the target bucket and topic exist, and that `salt_user_id` corresponds to a valid account where required.
- **Symptom: Bucket folder contains artifacts from many runs**: This is expected when reusing the same `folder_path`; the node differentiates runs via internal execution identifiers. If you want a fully separate location per run, include the run id or timestamp directly in `folder_path`.

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

