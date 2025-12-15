# RF Diffusion Scaffold Guided Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a configuration object for scaffold-guided and fold-conditioned protein design. It lets you enable scaffold-guided mode, optionally condition on a target protein (binder design), and specify directories and files for secondary structure and adjacency guidance.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionscaffoldguidedconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to prepare the scaffold-guided/fold-conditioning settings and connect its output to the 'scaffoldguided_config' input of the RF Diffusion node. Typical workflows: enabling scaffold-guided design with a scaffold directory, performing binder design by providing a target PDB (plus optional secondary-structure and adjacency files), or cropping specific residues from a scaffold to use as a starting template.

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
<tr><td style="word-wrap: break-word;">scaffoldguided</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable scaffold-guided mode. When true, the model expects secondary structure guidance for the designed backbone.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">target_pdb</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Set true if conditioning on a target protein structure (binder/protein–protein interaction design). Requires a valid target_path.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">target_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path to the target protein PDB file. Required when target_pdb is true.</td><td style="word-wrap: break-word;">/data/targets/1XYZ_target.pdb</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_dir</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Directory containing secondary structure (.pt) and block adjacency (.pt) files used for guided scaffolding.</td><td style="word-wrap: break-word;">/data/scaffolds/my_project_guidance/</td></tr>
<tr><td style="word-wrap: break-word;">sampled_insertion</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max residues to insert into loops during inference. Only applied if > 0.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">sampled_N</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the N-terminus. Only applied if > 0.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampled_C</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Max additional residues to sample at the C-terminus. Only applied if > 0.</td><td style="word-wrap: break-word;">7</td></tr>
<tr><td style="word-wrap: break-word;">target_ss</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to a secondary structure .pt file for the target protein. Optional; used with target_pdb.</td><td style="word-wrap: break-word;">/data/targets/1XYZ_target_ss.pt</td></tr>
<tr><td style="word-wrap: break-word;">target_adj</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to an adjacency .pt file for the target protein. Optional; used with target_pdb.</td><td style="word-wrap: break-word;">/data/targets/1XYZ_target_adj.pt</td></tr>
<tr><td style="word-wrap: break-word;">scaffold_list</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to a .txt file listing a subset of scaffold guidance files to use (when many are available).</td><td style="word-wrap: break-word;">/data/scaffolds/selection.txt</td></tr>
<tr><td style="word-wrap: break-word;">ss_mask</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of residues to mask at the end of a secondary structure block. Only applied if > 0.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">systematic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, scaffolds are iterated systematically rather than randomly sampled.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">mask_loops</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to mask loops. Set to false for protein–protein interaction design; when true, extra residues may be added without precise secondary structure.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">contig_crop</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue/chain selection to crop from a scaffold PDB as the starting template, using contig-like notation (e.g., 'A10-50', possibly multiple segments/chains).</td><td style="word-wrap: break-word;">A10-50/0 B1-25</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary to plug into the RF Diffusion node's scaffold-guided/fold conditioning input.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'target_pdb': True, 'target_path': '/data/targets/1XYZ_target.pdb', 'scaffold_dir': '/data/scaffolds/my_project_guidance/', 'sampled_insertion': 5, 'sampled_N': 3, 'mask_loops': False, 'contig_crop': [['A', 10, 50], ['/0'], ['B', 1, 25]]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Target conditioning**: If target_pdb is true, target_path must be a non-empty valid path or the node will raise an error.
- **Optional fields apply conditionally**: sampled_insertion, sampled_N, sampled_C, and ss_mask are only included if their values are greater than 0. systematic is included only when true; mask_loops is included only when set to false (since true is the default).
- **Scaffold directory**: scaffold_dir should contain compatible .pt files (secondary structure and adjacency) produced by appropriate preprocessing tools.
- **Contig cropping**: contig_crop must follow valid contig-like syntax (e.g., A10-50). Invalid formats will be rejected.
- **This node only configures**: It does not perform generation; connect its output to the RF Diffusion node's scaffoldguided_config input.

## Troubleshooting
- **Error: 'target_path is required when target_pdb is True'**: Provide a valid path in target_path or set target_pdb to false.
- **Invalid contig_crop format**: Use chain-letter and inclusive index ranges like 'A10-50' and separate segments/chains appropriately. Correct the string and try again.
- **No effect from sampled_* or ss_mask**: These parameters are only included when > 0. Increase from 0 to activate.
- **Unexpected behavior in PPI design**: For protein–protein interactions, set mask_loops to false as recommended.
- **Missing guidance files**: Ensure scaffold_dir exists and contains the expected .pt files; if conditioning on a target, verify target_ss/target_adj paths if you intend to use them.
