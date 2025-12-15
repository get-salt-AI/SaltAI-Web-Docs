# Equidock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein–protein docking of one or more ligand structures against a single receptor using the EquiDock model. It returns the ligand structure(s) transformed into the receptor’s coordinate frame, with an option to post-process to reduce steric clashes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/equidocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to dock protein ligand(s) to a receptor structure. Typical workflow: load or prepare ligand PDB(s) and a single receptor PDB, select the EquiDock checkpoint variant (dataset), optionally enable clash removal, then route the docked ligand output to downstream analysis or save steps.

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
<tr><td style="word-wrap: break-word;">ligand_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or multiple ligand structure files in PDB format to be docked. Provide as a mapping of names to PDB contents.</td><td style="word-wrap: break-word;">{'ligand_A.pdb': 'ATOM ...', 'ligand_B.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">receptor_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Single receptor structure in PDB format to dock against. Only one receptor is supported.</td><td style="word-wrap: break-word;">{'receptor.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">dataset</td><td>True</td><td style="word-wrap: break-word;">dips \| db5</td><td style="word-wrap: break-word;">Selects which EquiDock training checkpoint to use, corresponding to the dataset variant. Use the one matching your expected domain.</td><td style="word-wrap: break-word;">dips</td></tr>
<tr><td style="word-wrap: break-word;">remove_clashes</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies a post-processing step to reduce steric clashes in the docked ligands.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">bound_ligand.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Docked ligand structure(s) in PDB format, aligned to the receptor frame. If multiple ligands were provided, returns a mapping of names to docked PDB contents.</td><td style="word-wrap: break-word;">{'ligand_A_docked.pdb': 'ATOM ...', 'ligand_B_docked.pdb': 'ATOM ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Single receptor only**: The node rejects inputs with more than one receptor PDB.
- **Multiple ligands supported**: You can dock several ligands in one run; all will be aligned to the provided receptor.
- **Checkpoint selection**: The 'dataset' choice (dips or db5) determines which EquiDock checkpoint is used and can impact docking behavior.
- **Clash removal**: Enabling 'remove_clashes' may slightly adjust coordinates to resolve steric conflicts.

## Troubleshooting
- **Error: multiple receptors provided**: Provide exactly one receptor PDB; split multiple receptors into separate runs.
- **Invalid PDB input**: Ensure PDB strings are well-formed and non-empty; malformed PDB content may cause failures.
- **Unexpected docking quality**: Try changing the 'dataset' option (dips vs db5) or enabling 'remove_clashes' to improve results.
- **Output missing for some ligands**: Verify each ligand PDB is valid; remove problematic ligands and retry to isolate issues.
