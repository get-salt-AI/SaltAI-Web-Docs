# Load GCS Scaffold Folder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a lightweight reference to a Google Cloud Storage (GCS) folder that stores pre-computed scaffold .pt files for fold conditioning. It does not download any data; instead, it forwards the GCS folder path so downstream GPU services can load the files directly, typically for RF Diffusion-based workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/loadgcsscaffoldfoldernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when scaffold secondary structure and adjacency tensors are already saved in a GCS folder and you want to feed them into scaffold-guided protein design or fold-conditioning steps. Provide a valid GCS folder URI and connect the output to nodes that accept SCAFFOLD_PT_FILES; those downstream nodes will read the .pt files directly from GCS using the provided reference.

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
<tr><td style="word-wrap: break-word;">gcs_folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">GCS folder path that contains scaffold .pt file pairs (e.g., *_ss.pt and *_adj.pt). Must be a valid GCS URI starting with gs://. If missing, a trailing slash is automatically added so the path is treated as a folder.</td><td style="word-wrap: break-word;">gs://my-bucket/scaffolds/my_project_run/</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">A reference object that points to the specified GCS folder. Downstream nodes use this reference to locate and load scaffold .pt files directly from GCS without this node loading them into memory.</td><td style="word-wrap: break-word;">{"type": "gcs_path", "gcs_folder_path": "gs://my-bucket/scaffolds/my_project_run/"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: The folder should contain, for each scaffold, a pair of files: scaffold_name_ss.pt (secondary structure) and scaffold_name_adj.pt (block adjacency).
- **Note 2**: The node validates that the provided path starts with gs:// and normalizes it to end with a trailing slash so it is treated as a folder path.
- **Note 3**: No .pt files are listed, downloaded, or parsed by this node; it only passes a folder reference, which is suitable for large scaffold collections.
- **Note 4**: The node does not verify that the expected files exist; any missing or misnamed files will be surfaced as errors when downstream nodes attempt to read them.
- **Note 5**: Proper GCS permissions are required on the specified bucket and folder; permission or IAM issues will typically appear in downstream nodes that perform the actual file reads.

## Troubleshooting
- **Invalid GCS path format**: If you receive an error about the path, ensure it begins with gs:// and points to an existing bucket and folder (for example, gs://my-bucket/scaffolds/run_01/).
- **Downstream node cannot find *_ss.pt or *_adj.pt files**: Verify that the folder actually contains correctly named *_ss.pt and *_adj.pt file pairs for each scaffold, and that there are no typos in filenames.
- **Permission denied when accessing GCS**: Confirm that the credentials or service account for your environment have storage.objects.list and storage.objects.get permissions on the target bucket and folder.
- **Slow or timed-out reads in downstream steps**: Large folders can be affected by network or GCS performance issues; check connectivity, GCS status, and that the bucket region is appropriate for your compute region.
- **Path interpreted as a file instead of a folder**: Ensure that gcs_folder_path conceptually refers to a directory that contains .pt files. A trailing slash is added automatically, but the underlying GCS layout must represent a folder with scaffold files.
