# Equidock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein–protein docking using the EquiDock model. It docks one or more ligand structures into a single receptor structure and returns the ligand(s) transformed into the receptor frame. Optionally performs post-processing to reduce steric clashes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/equidocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to predict how a ligand protein binds to a receptor protein. Typical workflow: load ligand PDB(s) and a single receptor PDB with a loader node, choose the EquiDock training dataset variant (dips or db5) that matches your use case, optionally enable clash removal, then connect the output to downstream analysis or save steps.

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
<tr><td style="word-wrap: break-word;">ligand_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ligand structure(s) to dock. Supports one or multiple ligands. Usually provided by a Load PDB node as a mapping from filenames/IDs to PDB text.</td><td style="word-wrap: break-word;">{'ligand1.pdb': 'ATOM ...', 'ligand2.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">receptor_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Receptor structure to dock against. Must contain exactly one receptor entry.</td><td style="word-wrap: break-word;">{'receptor.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">dataset</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">EquiDock checkpoint variant used for inference. Select the dataset the model was trained on.</td><td style="word-wrap: break-word;">dips</td></tr>
<tr><td style="word-wrap: break-word;">remove_clashes</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, post-processes docked ligands to mitigate steric clashes.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">bound_ligand.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Docked ligand structure(s) transformed into the receptor coordinate frame. If multiple ligands were provided, returns all corresponding docked ligands.</td><td style="word-wrap: break-word;">{'ligand1_docked.pdb': 'ATOM ...', 'ligand2_docked.pdb': 'ATOM ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Single receptor only**: The node does not support multiple receptors; provide exactly one receptor PDB entry.
- **Multiple ligands supported**: You may input more than one ligand; outputs will include all docked ligands.
- **Dataset choice**: Selecting the appropriate dataset variant (dips or db5) can affect performance; use the one aligned with your target domain.
- **Input format**: PDB inputs are expected as a mapping from a name (e.g., filename) to PDB text, typically sourced from a PDB loader node.

## Troubleshooting
- **Error: multiple receptors provided**: Ensure the receptor_pdb input contains exactly one entry. Split receptors into separate runs if needed.
- **Empty or failed docking result**: Verify that the input PDB files are valid and properly formatted; remove non-standard residues or alternate locations if necessary.
- **Unexpected geometry after clash removal**: Disable remove_clashes to check the raw docking result; then adjust preprocessing or try the alternative dataset setting.
- **Output naming confusion**: If multiple ligands were input, check output keys (e.g., ligand1_docked.pdb, ligand2_docked.pdb) to map each result to its corresponding input.
