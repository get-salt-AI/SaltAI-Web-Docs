# RF Diffusion Scaffold Guided Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the scaffold-guided (fold conditioning) configuration for RF Diffusion. It packages scaffold PDBs, optional target protein data, and tuning parameters (e.g., loop masking, terminal residue sampling) into a single config object. When connected, it enables scaffold-guided mode in RF Diffusion, allowing contigs to be auto-derived from the scaffold.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want RF Diffusion to follow a specific fold/topology from one or more scaffold structures, optionally targeting a partner protein for PPI design. Connect the output to the 'scaffoldguided_config' input of the RF Diffusion node. Typical workflow: provide scaffold PDBs (from PDB Loader/Combiner), optionally add a target protein (and its PDBs for secondary structure extraction), and fine-tune settings like loop masking, terminal sampling, and optional cropping.

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
<tr><td style="word-wrap: break-word;">scaffold_pdbs</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more scaffold structures used to define the fold/topology for conditioning. Provide via PDB Loader or PDB Combiner.</td><td style="word-wrap: break-word;">{'scaffold1.pdb': '<pdb-content>', 'scaffold2.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A target protein structure used for conditioned binder (PPI) design. When provided, also include 'target_pdbs' for secondary structure computation.</td><td style="word-wrap: break-word;">{'target.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdbs</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more PDBs for the target protein to compute secondary structure/adjacency. Strongly recommended when 'target_pdb' is set.</td><td style="word-wrap: break-word;">{'target_variant1.pdb': '<pdb-content>', 'target_variant2.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text content listing scaffold names (one per line) to restrict which scaffolds are used. Lines starting with '#' are ignored. Leave empty to use all provided scaffolds.</td><td style="word-wrap: break-word;">scaffold1.pdb scaffold3.pdb # comment line</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max residues to insert into loops during inference. Set >0 to allow loop extension.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the N-terminus. Set >0 to enable.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the C-terminus. Set >0 to enable.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of residues to mask at the end of a secondary structure block. Set >0 to enable.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, iterates systematically through scaffolds instead of random sampling.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to mask loops. For binder/PPI design, set to false. Default is true.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Crop specification to select residues/chains from a scaffold as the starting template. Format similar to contigs (e.g., 'A10-50').</td><td style="word-wrap: break-word;">A10-50</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Configuration object enabling scaffold-guided/fold conditioning in RF Diffusion. Connect to the RF Diffusion node's 'scaffoldguided_config' input.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'scaffold_secstruc_adj': {'pdbs': {'scaffold1.pdb': '<pdb-content>'}, 'type': 'secstruc_adj_input'}, 'mask_loops': False, 'sampled_insertion': 5, 'contig_crop': 'A10-50'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Scaffold-guided mode**: Connecting this node enables scaffold-guided mode in RF Diffusion, allowing contigs to be empty or auto-derived from the scaffold.
- **PPI and hotspots**: If you plan to use hotspot residues in RF Diffusion for PPI design, you must provide a target protein here (connect 'target_pdb' and usually 'target_pdbs').
- **Target inputs**: Providing 'target_pdb' without 'target_pdbs' is allowed but not recommended; secondary structure data is better with 'target_pdbs'.
- **Loop masking**: For binder/PPI workflows, set 'mask_loops' to false. Loop masking is primarily for scaffold-only fold conditioning.
- **Conditional enabling**: Numeric options (sampled_insertion, sampled_N, sampled_C, ss_mask) are included only when set to values > 0.
- **Cropping**: 'contig_crop' follows contig-like syntax; invalid formats will be rejected.

## Troubleshooting
- **Missing scaffold_pdbs**: If you see an error that scaffold_pdbs is required, ensure you connected one or more PDBs from PDB Loader or PDB Combiner.
- **Hotspots error in RF Diffusion**: If RF Diffusion errors that hotspots were provided without a target protein, connect 'target_pdb' (and 'target_pdbs') in this node.
- **Invalid contig_crop**: If contig_crop is rejected, verify the format (e.g., 'A10-50') and that chain IDs and ranges match the scaffold.
- **Unexpected loop behavior**: If designs overly constrain loops, disable loop masking by setting 'mask_loops' to false for PPI workflows.
- **No scaffolds used from list**: If nothing is selected, confirm 'scaffold_list' lines match keys/names of provided scaffold PDBs and that comment lines start with '#'.
