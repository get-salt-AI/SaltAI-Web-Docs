# RF Diffusion Scaffold Guided Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node configures scaffold-guided (fold-conditioned) RF Diffusion runs by combining precomputed secondary-structure and adjacency tensors for one or more scaffolds with optional target protein and target fold information. It supports GCS-hosted and inline .pt files, scaffold subsets, loop and terminus sampling, and masking/cropping options. The output is a configuration object consumed by the RF Diffusion node through its scaffoldguided_config input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when designing proteins constrained to specific folds or scaffold libraries, with or without a target protein for binder/PPI design. Typical pattern: upstream, obtain scaffold_pt_files from a node that loads a GCS folder or produces *_ss.pt and *_adj.pt files; connect scaffold_pt_files here. Optionally provide target_pdb (and target_pt_files) for binder design, and scaffold_list to restrict to selected scaffolds. Tune sampled_insertion, sampled_N, sampled_C, ss_mask, systematic, mask_loops, and contig_crop to control flexibility and region selection. Then connect the resulting scaffoldguided_config output into SaaSRFDiffusionNode’s scaffoldguided_config input, alongside other optional config nodes like SaaSRFDiffusionContigmapConfigNode or SaaSRFDiffusionPotentialsConfigNode. Prefer gcs_path mode for larger libraries and keep sampling/masking modest when you need to preserve scaffold topology.

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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td>True</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Precomputed scaffold secondary-structure and adjacency tensors for fold conditioning. Accepted formats: an object with type="gcs_path" and gcs_folder_path pointing to a GCS folder of *_ss.pt and *_adj.pt files; an object with type="inline" and pt_files mapping filenames to encoded .pt content; or a legacy dict mapping filenames directly to inline .pt content. Must be non-empty.</td><td style="word-wrap: break-word;">{"type": "gcs_path", "gcs_folder_path": "gs://protein-scaffolds/helix_bundle_secstruc_adj"}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional target protein PDB for binder or protein-protein interaction design. Provides the 3D structure of the protein that the scaffolded design should bind. The node extracts the first PDB structure and marks target_pdb as enabled in the configuration.</td><td style="word-wrap: break-word;">{"filename": "target_egfr.pdb", "content": "ATOM      1  N   MET A   1 ..."}</td></tr>
<tr><td style="word-wrap: break-word;">target_pt_files</td><td>False</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Optional precomputed secondary-structure and adjacency .pt files for the target protein. Same formats as scaffold_pt_files (gcs_path or inline). If present, adds target_secstruc_adj to the configuration for target fold conditioning.</td><td style="word-wrap: break-word;">{"type": "inline", "pt_files": {"egfr_target_ss.pt": "<base64-ss-data>", "egfr_target_adj.pt": "<base64-adj-data>"}}</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional newline-separated list of scaffold names to use from scaffold_pt_files. Each non-empty, non-comment line is a scaffold identifier; lines starting with # are ignored. Leave empty to use all scaffolds.</td><td style="word-wrap: break-word;">helical_bundle_1 helical_bundle_3 # skip helical_bundle_2 beta_barrel_A</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of residues that can be inserted into loop regions per design. Values > 0 enable variable loop lengths while preserving scaffold topology. Valid range 0–50; 0 disables loop-length sampling.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum additional residues to sample at the N terminus. Values > 0 allow variable N-terminal extensions. Valid range 0–50; 0 keeps the N terminus fixed to the scaffold length.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum additional residues to sample at the C terminus. Values > 0 allow variable C-terminal extensions. Valid range 0–50; 0 keeps the C terminus fixed to the scaffold length.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of residues at the end of each secondary-structure block (helix or strand) to mask, allowing flexibility at block boundaries. Valid range 0–20. 0 means no extra masking beyond loops.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, RF Diffusion iterates through scaffolds in a systematic, deterministic order instead of sampling randomly from the available set. Use when you want exhaustive or ordered exploration of a scaffold library.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether loop regions of the scaffold are masked for flexible redesign. True (default) masks loops so they can change; false preserves loop geometry, which is often desirable when loops are functionally important in binding.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contig-style crop specification to focus on a subregion of the scaffold before conditioning. Must follow RF Diffusion contig syntax, such as A10-50 for residues 10–50 of chain A. Empty string means use the full scaffold.</td><td style="word-wrap: break-word;">A35-120</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Configuration object encoding scaffold-guided and fold-conditioning settings for RF Diffusion. It always includes scaffoldguided set to true and a scaffold_secstruc_adj section with either a GCS folder path or an inline pt_files map. If target_pdb is provided, it includes target_pdb and target_pdb_content, and if target_pt_files are given, a target_secstruc_adj section. Depending on inputs it may also include scaffold_list, sampled_insertion, sampled_N, sampled_C, ss_mask, systematic, mask_loops, and contig_crop.</td><td style="word-wrap: break-word;">{"scaffoldguided": true, "scaffold_secstruc_adj": {"type": "gcs_path", "gcs_folder_path": "gs://protein-scaffolds/helix_bundle_secstruc_adj"}, "target_pdb": true, "target_pdb_content": "ATOM      1  N   MET A   1 ...", "target_secstruc_adj": {"type": "inline", "pt_files": {"egfr_target_ss.pt": "<base64-ss-data>", "egfr_target_adj.pt": "<base64-adj-data>"}}, "scaffold_list": ["helical_bundle_1", "helical_bundle_3"], "sampled_insertion": 10, "sampled_N": 5, "sampled_C": 8, "ss_mask": 2, "systematic": true, "mask_loops": false, "contig_crop": [["A", 35, 120]]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large scaffold libraries or many inline pt_files increase payload size and can slow down RF Diffusion startup; use gcs_path references instead of inlining when handling many scaffolds.
- **Limitations**: scaffold_pt_files must be a non-empty mapping. If the type field is missing or unrecognized, the node assumes a legacy inline format; if the structure does not match this, the downstream RF Diffusion service can fail at runtime.
- **Behavior**: The node always sets scaffoldguided to true in the output. In scaffold-guided mode the downstream RF Diffusion node can infer layout from scaffold structure, relaxing the need for explicit contigs compared to non-scaffold-guided runs.
- **Behavior**: sampled_insertion, sampled_N, sampled_C, and ss_mask are only present in the config when greater than zero, and systematic and mask_loops only appear when they differ from default behavior; do not rely on their absence meaning they were explicitly set to zero or false.
- **Integration with hotspots**: In SaaSRFDiffusionNode, hotspot_res is only valid when scaffoldguided mode is active and a target_pdb is configured via this node; specifying hotspot_res without a target causes a validation error downstream.
- **Data handling**: For target_pdb, only the first PDB entry is used; multi-model or concatenated PDB inputs are silently reduced to a single structure, so ensure the desired model is first.

## Troubleshooting
- **Common Error 1**: "ValueError: scaffold_pt_files is required. Connect from 'Load GCS Scaffold Folder' or 'Make Secstruc Adj' node." This means scaffold_pt_files is missing or empty. Fix by connecting a valid scaffold .pt source and confirming it returns a gcs_path descriptor or a non-empty pt_files map.
- **Common Error 2**: RF Diffusion fails complaining about invalid or missing contigs despite expecting scaffold-guided behavior. Verify that this node’s output is connected to SaaSRFDiffusionNode.scaffoldguided_config and that no incompatible contigmap configuration is forcing inconsistent layouts.
- **Common Error 3**: Hotspot-related validation error in SaaSRFDiffusionNode indicating hotspot_res was provided but no target protein configured. Either connect a target_pdb here or clear hotspot_res in SaaSRFDiffusionNode when not designing binders.
- **Common Error 4**: Scaffolds used in designs do not match expectations when using scaffold_list. Check that list entries exactly match scaffold identifiers, remove trailing spaces, and ensure comment lines start with # so they are ignored rather than interpreted as names.
