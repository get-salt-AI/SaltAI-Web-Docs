# Load Cloud Scaffold Folder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node registers a Google Cloud Storage (GCS) folder that contains precomputed scaffold .pt files used for RFDiffusion scaffold-guided protein design. It validates and normalizes the GCS path, then returns a structured reference object instead of loading data into memory. The actual *_ss.pt and *_adj.pt files are later loaded directly from GCS by GPU-side services using this reference.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/loadgcsscaffoldfoldernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have secondary structure and block adjacency .pt files uploaded to a GCS bucket and want to use them as scaffolds in protein design workflows. It typically appears early in a scaffold-guided RFDiffusion pipeline, where it prepares the scaffold reference that is passed into downstream nodes performing fold conditioning or structure generation. A common pattern is: (1) generate *_ss.pt and *_adj.pt files with RFDiffusion's make_secstruc_adj.py, (2) upload them to a dedicated GCS folder, and (3) configure this node with that folder path. Downstream biotech/RFDiffusion nodes that accept SCAFFOLD_PT_FILES as input then consume the scaffold_pt_files reference returned by this node. Because the node only passes a reference, it scales efficiently to large scaffold libraries and multi-GB folders. Organize scaffolds in clearly named subfolders (for example, gs://your-bucket/scaffolds/projectA_run1/) to keep configurations stable and reusable across experiments.

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
<tr><td style="word-wrap: break-word;">gcs_folder_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fully-qualified GCS folder URI containing scaffold .pt files. Must start with "gs://" and point to a directory that holds paired RFDiffusion outputs: scaffold_name_ss.pt (secondary structure tensor) and scaffold_name_adj.pt (block adjacency matrix). The node trims whitespace and ensures the path ends with a trailing slash before returning it. The folder must be readable by the environment's GCS credentials; missing permissions will surface in downstream services when they attempt to load the files.</td><td style="word-wrap: break-word;">gs://emi-file-uploads/scaffolds/rfdiffusion_run_2025_03_10/</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">A reference object that tells downstream GPU services where to find scaffold .pt files in GCS. It does not embed the tensors themselves; instead it includes the reference type and normalized folder path. Nodes that implement RFDiffusion fold conditioning use this structure to stream *_ss.pt and *_adj.pt files directly from GCS at runtime.</td><td style="word-wrap: break-word;">{'type': 'gcs_path', 'gcs_folder_path': 'gs://emi-file-uploads/scaffolds/rfdiffusion_run_2025_03_10/'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node never lists or loads files; it only returns a small reference object, so it adds negligible overhead even when pointing to very large folders.
- **Limitations**: Only GCS URIs are allowed; the path must start with "gs://". Local filesystem paths or HTTPS URLs will cause a validation error in this node.
- **Behavior**: If the provided gcs_folder_path does not end with a slash, the node automatically appends "/" before returning it, guaranteeing a normalized folder path for downstream services.
- **Behavior**: The node does not verify that *_ss.pt and *_adj.pt files actually exist or are correctly paired; any missing or malformed files will only be detected later when GPU-side RFDiffusion services try to read from the folder.

## Troubleshooting
- **Invalid GCS path format. Expected gs://bucket/path, got: ...**: This indicates gcs_folder_path does not start with "gs://". Update the input to a valid GCS URI such as "gs://my-bucket/scaffolds/run1/" and try again.
- **Downstream node cannot load scaffold .pt files**: If later nodes report missing files or permission errors, confirm that the folder contains correctly named scaffold_name_ss.pt and scaffold_name_adj.pt pairs and that your Salt environment has read access to the target GCS bucket.
- **Unexpected path behavior due to missing slash**: If logs suggest incorrect path concatenation, ensure that the configured path is correct. The node appends a trailing slash automatically, so paths like "gs://my-bucket/scaffolds" are turned into "gs://my-bucket/scaffolds/" in the reference.
- **Scaffold conditioning appears to have no effect**: If generations look unconditioned, verify that the downstream RFDiffusion-related node is wired to the scaffold_pt_files output of this node and that the target GCS folder contains valid .pt files generated by make_secstruc_adj.py. Also double-check that you are using a node variant that accepts SCAFFOLD_PT_FILES as an input.
