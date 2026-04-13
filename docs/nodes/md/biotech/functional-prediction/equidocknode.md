# Equidock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein–protein docking using the EquiDock model. It docks one or more ligand protein structures into a single receptor protein structure and returns the ligand(s) transformed into the receptor coordinate frame. Optionally, it performs post-processing to reduce steric clashes in the docked complexes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/equidocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to predict how a ligand protein binds to a receptor protein in 3D. A typical workflow is: load one or more ligand PDBs and a single receptor PDB with a PDB loader node, choose the EquiDock dataset variant (e.g., dips or db5) that best matches your application domain, optionally enable clash removal, then feed the docked ligand output into downstream structural analysis, scoring, visualization, or file-saving nodes.

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
<tr><td style="word-wrap: break-word;">ligand_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ligand protein structure(s) to dock. Supports one or multiple ligands, provided as a mapping from identifiers (such as filenames) to PDB text strings. Typically sourced from a PDB loader node.</td><td style="word-wrap: break-word;">{'ligand1.pdb': 'ATOM ...', 'ligand2.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">receptor_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Receptor protein structure to dock against. Must contain exactly one receptor entry, provided as a mapping from a single identifier to PDB text.</td><td style="word-wrap: break-word;">{'receptor.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">dataset</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">EquiDock checkpoint/dataset variant to use for inference. Choose the dataset (e.g., dips, db5) corresponding to the training data distribution most relevant to your use case.</td><td style="word-wrap: break-word;">dips</td></tr>
<tr><td style="word-wrap: break-word;">remove_clashes</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to apply a post-processing step to reduce steric clashes in the docked ligand structures. Set to true for cleaner complexes, or false to inspect the raw EquiDock output.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">bound_ligand.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Docked ligand protein structure(s) transformed into the receptor coordinate frame. If multiple ligands were provided, returns a mapping of identifiers to docked PDB text for each ligand.</td><td style="word-wrap: break-word;">{'ligand1_docked.pdb': 'ATOM ...', 'ligand2_docked.pdb': 'ATOM ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Single receptor only**: The node requires exactly one receptor structure. It does not support multiple receptors in a single run; split them across multiple executions if needed.
- **Multiple ligands supported**: You can provide multiple ligand structures at once, and the node will return a docked result for each ligand.
- **Dataset selection matters**: Choosing the correct dataset variant (such as dips or db5) can affect docking quality and generalization; align this with the type of complexes you are modeling.
- **Input format**: Both ligand and receptor PDBs must be provided as mappings from names (e.g., filenames or IDs) to PDB text strings, typically produced by an upstream PDB loader node.
- **Post-processing behavior**: When remove_clashes is enabled, coordinates may be adjusted to reduce clashes, which can slightly alter the original predicted pose.

## Troubleshooting
- **Error about multiple receptors or ambiguous receptor input**: Ensure receptor_pdb contains exactly one entry. If you have multiple receptors, run the node separately for each one.
- **No or empty docking output**: Confirm that all input PDBs are valid, properly formatted, and contain standard protein atoms. Remove problematic records such as non-standard residues or malformed lines.
- **Docked geometry appears unrealistic**: Try disabling remove_clashes to inspect the raw EquiDock prediction. If issues persist, experiment with the alternative dataset setting or refine the input structures (e.g., clean heteroatoms, fix missing residues).
- **Confusion mapping outputs to inputs**: For multi-ligand runs, check the keys in the output mapping (e.g., ligand1_docked.pdb) and align them with the corresponding ligand keys in the input mapping.
- **Performance or quality not as expected**: Verify that you selected the dataset variant best matching your target domain (e.g., similar to your receptor–ligand types) and that the receptor truly corresponds to the ligands being docked.
