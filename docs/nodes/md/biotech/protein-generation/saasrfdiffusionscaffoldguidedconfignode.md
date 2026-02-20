# RF Diffusion Scaffold Guided Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the scaffold-guided (fold-conditioning) configuration for RF Diffusion. It connects pre-computed secondary structure and block adjacency tensors for scaffolds (and optionally a target protein) to guide design toward desired topologies. Supports both GCS folder references and inline .pt file payloads, with options for loop masking, residue insertions at loops/termini, and selective scaffold usage.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to condition generation on known fold topologies or perform binder design against a target protein with fold constraints. Typical workflow: 1) Load scaffold .pt files (e.g., via a loader node providing SCAFFOLD_PT_FILES), 2) Optionally provide a target PDB and its .pt files for PPI or target fold conditioning, 3) Optionally restrict to a list of scaffold names and tune sampling/loop settings, 4) Connect the output to the 'scaffoldguided_config' input of the RF Diffusion node.

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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td>True</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Pre-computed scaffold .pt files containing secondary structure (*_ss.pt) and block adjacency (*_adj.pt) tensors. Supports either a GCS folder reference or inline payloads.</td><td style="word-wrap: break-word;">{"type":"gcs_path","gcs_folder_path":"<gcs-folder-path>"} or {"type":"inline","pt_files":{"scaffoldA_ss.pt":"<base64>","scaffoldA_adj.pt":"<base64>"}}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional target protein PDB for conditioned binder (PPI) design. Enables hotspot residue constraints at the RF Diffusion node.</td><td style="word-wrap: break-word;">{"filename":"target.pdb","content":"<pdb-text>"}</td></tr>
<tr><td style="word-wrap: break-word;">target_pt_files</td><td>False</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Optional target .pt files for target fold conditioning. Same supported formats as scaffold_pt_files.</td><td style="word-wrap: break-word;">{"type":"gcs_path","gcs_folder_path":"<gcs-target-folder>"} or {"type":"inline","pt_files":{"target_ss.pt":"<base64>","target_adj.pt":"<base64>"}}</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One scaffold name per line to restrict which scaffolds are used. Leave empty to use all provided scaffolds. Lines starting with # are ignored.</td><td style="word-wrap: break-word;">scaffold_1 scaffold_2 # comment scaffold_3</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max residues to insert into loop regions per run. Enables flexible loop lengths while preserving secondary structure.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the N-terminus.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the C-terminus.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Residues to mask at the end of each secondary structure block, allowing flexibility at helix/strand boundaries.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, iterate through scaffolds in a fixed order instead of random sampling.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, loop regions are masked to encourage flexible redesign. Set false for PPI when loops should be preserved.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Crop a specific region from the scaffold using chain and residue range format. Leave empty for full scaffold.</td><td style="word-wrap: break-word;">A10-50</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Configuration object to drive scaffold-guided/fold-conditioning behavior in RF Diffusion.</td><td style="word-wrap: break-word;">{"scaffoldguided":true,"scaffold_secstruc_adj":{"type":"gcs_path","gcs_folder_path":"<gcs-folder-path>"},"scaffold_list":["scaffold_1","scaffold_2"],"sampled_insertion":10}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Scaffold .pt inputs support two modes: gcs_path (service loads from cloud storage) and inline (base64-encoded pt files). A legacy inline dict of pt files is also accepted.
- In scaffold-guided mode, sequence contigs can be auto-derived at runtime; you don't need to define contigs for the main generation node.
- If you plan to use hotspot residues in the generation node (for PPI), you must provide a target_pdb here; otherwise hotspot_res will be rejected.
- mask_loops defaults to true for flexible loop redesign. For binder design that must preserve target-facing loops, set mask_loops to false.
- scaffold_list is parsed line-by-line; empty lines and lines starting with # are ignored.
- contig_crop must follow chain/range formatting (e.g., A10-50). Invalid formats will cause validation errors downstream.

## Troubleshooting
- Error: 'scaffold_pt_files is required' — Provide a non-empty SCAFFOLD_PT_FILES input, either as a GCS folder reference or inline pt_files.
- Hotspot residues were specified but no target protein is configured — Add a target_pdb here or clear hotspot_res in the generation node.
- Invalid contig_crop format — Use chain and inclusive residue range like 'A10-50'.
- No scaffolds used after providing scaffold_list — Ensure the names match those present in the .pt set and that lines are not commented out.
- Inline mode failing due to payload — Ensure pt_files includes both *_ss.pt and *_adj.pt entries for each scaffold, base64-encoded.
- GCS path not accessible — Verify the gcs_folder_path and permissions for the GPU service to read the folder.
