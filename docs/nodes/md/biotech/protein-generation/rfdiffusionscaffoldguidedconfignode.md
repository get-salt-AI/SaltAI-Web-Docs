# EMI: RF Diffusion Scaffold Guided Config (DEPRECATED)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the scaffold-guided (fold-conditioning) configuration for RF Diffusion. It connects pre-computed scaffold secondary structure and adjacency tensors (and optionally a target protein and its tensors) to guide protein design toward specific fold topologies. Supports GCS folder references and inline .pt payloads with controls for loop masking, residue insertions, and scaffold selection.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/rfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want RF Diffusion to follow predefined scaffold folds or design binders against a target protein under fold constraints. Typical workflow: provide scaffold .pt files as SCAFFOLD_PT_FILES (from GCS or inline), optionally add a target PDB and target .pt files, optionally restrict or prioritize specific scaffolds and adjust loop/termini flexibility, then connect this node’s output to the `scaffoldguided_config` input of the RF Diffusion node.

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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td>True</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Pre-computed scaffold .pt files containing secondary structure (*_ss.pt) and block adjacency (*_adj.pt) tensors. Can be provided as a GCS folder reference for remote loading or as inline base64-encoded .pt file payloads. Defines the scaffold folds used for conditioning.</td><td style="word-wrap: break-word;">{"type":"gcs_path","gcs_folder_path":"<gcs-folder-path>"} or {"type":"inline","pt_files":{"scaffoldA_ss.pt":"<base64>","scaffoldA_adj.pt":"<base64>"}}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional target protein PDB for conditioned binder (PPI) design or to provide a structural target context. Required if hotspot residues will be used in the RF Diffusion node.</td><td style="word-wrap: break-word;">{"filename":"target.pdb","content":"<pdb-text>"}</td></tr>
<tr><td style="word-wrap: break-word;">target_pt_files</td><td>False</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Optional target .pt files (secondary structure and adjacency tensors) for target fold conditioning. Uses the same formats as scaffold_pt_files (GCS folder reference or inline base64-encoded *_ss.pt and *_adj.pt files).</td><td style="word-wrap: break-word;">{"type":"gcs_path","gcs_folder_path":"<gcs-target-folder>"} or {"type":"inline","pt_files":{"target_ss.pt":"<base64>","target_adj.pt":"<base64>"}}</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional text list of scaffold names to restrict which scaffolds from the .pt set are used. One name per line is recommended. Empty value means all scaffolds are eligible. Lines starting with '#' are treated as comments and ignored.</td><td style="word-wrap: break-word;">scaffold_1 scaffold_2 # comment scaffold_3</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of residues that may be inserted into loop regions per run. Enables variable loop lengths while preserving the overall secondary structure layout.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of additional residues that may be sampled at the N-terminus of the designed protein.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of additional residues that may be sampled at the C-terminus of the designed protein.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of residues to mask at the end of each secondary structure block, creating flexible boundaries at helix/strand ends while maintaining the fold.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, iterates through scaffolds in a fixed deterministic order instead of random sampling. Useful for systematically covering a scaffold library.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls treatment of loop regions. If true, loop regions are masked to encourage flexible redesign; if false, loops are preserved as in the scaffold (often preferred for some binder/PPI tasks).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional directive to crop a sub-region from the scaffold using chain and residue range format. Only the specified segment is used for conditioning. Leave empty to use the full scaffold.</td><td style="word-wrap: break-word;">A10-50</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Configuration object encoding scaffold-guided and fold-conditioning settings, including scaffold and optional target sources plus loop/termini sampling parameters. Connect this to the `scaffoldguided_config` input of the RF Diffusion node to enable scaffold-guided design.</td><td style="word-wrap: break-word;">{"scaffoldguided":true,"scaffold_secstruc_adj":{"type":"gcs_path","gcs_folder_path":"<gcs-folder-path>"},"scaffold_list":["scaffold_1","scaffold_2"],"sampled_insertion":10}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: SCAFFOLD_PT_FILES supports `gcs_path` (service-side loading from a GCS folder) and `inline` (base64-encoded pt_files); a legacy inline dict format is also accepted as long as it contains *_ss.pt and *_adj.pt pairs.
- **Note 2**: In scaffold-guided mode, RF Diffusion can derive contigs automatically from the scaffold configuration, so explicit contigs on the RF Diffusion node are often unnecessary.
- **Note 3**: If you configure hotspot residues in RF Diffusion, a `target_pdb` must be provided here; otherwise hotspot settings will be rejected due to missing structural context.
- **Note 4**: `mask_loops` is typically set to true for flexible loop redesign. For binder designs where loop geometry must be preserved at an interface, set `mask_loops` to false.
- **Note 5**: `scaffold_list` is parsed line-by-line; empty lines and lines starting with `#` are ignored, allowing you to comment out specific scaffolds without deleting them.
- **Note 6**: `contig_crop` must be a single chain-plus-range specification such as `A10-50`. Malformed strings or multiple ranges can lead to validation errors downstream.
- **Note 7**: For inline payloads, every scaffold or target must provide both *_ss.pt and *_adj.pt entries with valid base64-encoded contents; missing or misnamed files will cause configuration failures.

## Troubleshooting
- **Issue 1**: Error 'scaffold_pt_files is required'. Resolution: Connect `scaffold_pt_files` and provide either a valid `gcs_path` pointing to a folder with *_ss.pt and *_adj.pt files, or an `inline` payload containing correctly named base64-encoded .pt files.
- **Issue 2**: Hotspot residues configured in RF Diffusion but run fails or rejects them. Resolution: Add a `target_pdb` (and optionally `target_pt_files`) here so hotspots have a structural target, or remove hotspot settings from the RF Diffusion node.
- **Issue 3**: Validation error for `contig_crop`. Resolution: Use a single segment format such as `A10-50` with no spaces or multiple segments. Correct the string and retry.
- **Issue 4**: No scaffolds appear to be used after setting `scaffold_list`. Resolution: Confirm that scaffold names exactly match those in your .pt files, including case and spelling, and make sure lines are not commented out with `#`.
- **Issue 5**: Inline mode fails due to payload or decoding problems. Resolution: Ensure `pt_files` includes both *_ss.pt and *_adj.pt for each scaffold or target, that all values are valid base64 encodings of .pt files, and that no required keys are missing.
- **Issue 6**: GCS folder cannot be read or scaffolds missing. Resolution: Verify `gcs_folder_path`, confirm the bucket/folder contains the expected *_ss.pt and *_adj.pt pairs, and check that the executing service account has read access to the GCS location.
