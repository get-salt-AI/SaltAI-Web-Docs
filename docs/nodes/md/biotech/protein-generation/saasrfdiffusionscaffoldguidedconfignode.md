# RF Diffusion Scaffold Guided Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a scaffold-guided (fold-conditioned) configuration for RF Diffusion protein design. It bundles scaffold PDBs, optional target protein inputs for binder design, loop-masking behavior, residue sampling at termini/loops, and optional contig cropping into a single configuration object.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want RF Diffusion to follow a specific fold/topology using scaffold structures. Connect scaffold PDBs (from a PDB Loader or PDB Combiner). For protein-protein interaction design, also supply a target PDB and the target PDBs used to derive secondary structure. Feed the resulting configuration into the RF Diffusion node’s scaffoldguided_config input.

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
<tr><td style="word-wrap: break-word;">scaffold_pdbs</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of scaffold PDB structure(s) used to impose fold conditioning. Keys are names; values are PDB file contents.</td><td style="word-wrap: break-word;">{'scaffold_1.pdb': '<pdb-file-content>', 'scaffold_2.pdb': '<pdb-file-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Single target protein PDB for conditioned binder design. When provided, it is recommended to also provide target_pdbs for computing target secondary structure.</td><td style="word-wrap: break-word;">{'target.pdb': '<pdb-file-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">target_pdbs</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of target PDB structure(s) used to compute secondary structure for the target (recommended when target_pdb is set).</td><td style="word-wrap: break-word;">{'target_ref_1.pdb': '<pdb-file-content>', 'target_ref_2.pdb': '<pdb-file-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Content of a scaffold list (one scaffold name per line). When provided, only listed scaffolds are used. Lines starting with '#' are treated as comments.</td><td style="word-wrap: break-word;">scaffold_1.pdb scaffold_2.pdb # comment line</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of residues to insert into loops per run to extend or adjust secondary structures. Applied only if > 0.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of additional residues to sample at the N terminus. Applied only if > 0.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of additional residues to sample at the C terminus. Applied only if > 0.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of residues to mask at the end of each secondary structure block (0–20). Applied only if > 0.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, iterates through scaffolds systematically rather than randomly sampling.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to mask loops. True allows extra residues without precise structures; typically set False for PPI designs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue/chain crop spec to select a subset from the scaffold as the starting template. Uses contig-like syntax (e.g., 'A10-50', optionally multiple segments).</td><td style="word-wrap: break-word;">A10-50;B5-30</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">A configuration object enabling scaffold-guided mode with secondary structure/adjacency inputs, optional target conditioning, sampling and masking parameters. Connect this to the RF Diffusion node’s scaffoldguided_config input.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'scaffold_secstruc_adj': {'type': 'secstruc_adj_input', 'pdbs': {'scaffold_1.pdb': '<pdb-file-content>'}}, 'mask_loops': False, 'sampled_insertion': 5, 'sampled_N': 3, 'sampled_C': 2, 'ss_mask': 4, 'systematic': True, 'contig_crop': 'A10-50', 'target_pdb': True, 'target_pdb_content': '<pdb-file-content>', 'target_secstruc_adj': {'type': 'secstruc_adj_input', 'pdbs': {'target_ref_1.pdb': '<pdb-file-content>'}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Provide scaffold_pdbs as a dictionary mapping names to PDB contents; at least one scaffold is required.
- For binder (PPI) designs, supply target_pdb and, ideally, target_pdbs to compute target secondary structure.
- Loop and termini sampling parameters (sampled_insertion, sampled_N, sampled_C) are applied only when greater than zero.
- Set mask_loops to False for typical PPI workflows to avoid masking loops on the target interface.
- contig_crop must follow contig-like residue selection syntax (e.g., A10-50). Invalid specs will be rejected.
- scaffold_list filters which scaffolds are used; ensure listed names match the keys in scaffold_pdbs.

## Troubleshooting
- Error: 'scaffold_pdbs is required' — Ensure scaffold_pdbs is connected and is a non-empty dictionary of name->PDB content.
- Warning: target_pdb provided without target_pdbs — The config can still run, but provide target_pdbs for better target secondary structure derivation.
- No effect from sampling parameters — Confirm sampled_insertion, sampled_N, or sampled_C are set to values > 0.
- Invalid contig_crop format — Use chainID followed by residue ranges (e.g., 'A10-50'). Remove spaces and ensure chain IDs exist in the scaffold.
- Scaffolds not filtered by scaffold_list — Verify each line matches a key in scaffold_pdbs and that comment lines start with '#' only.
