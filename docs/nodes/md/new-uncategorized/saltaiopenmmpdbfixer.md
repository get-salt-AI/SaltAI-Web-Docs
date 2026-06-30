# OpenMM PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node prepares real-world PDB structures for OpenMM simulation by repairing common structural issues before solvation, minimization, or molecular dynamics. It standardizes residues, adds missing atoms and hydrogens at a chosen pH, and gives explicit control over ligands, cofactors, metals, and water molecules so they are not silently removed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiopenmmpdbfixer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use **OpenMM PDB Fixer** immediately after loading or generating a PDB structure and before **OpenMM Solvate**, **OpenMM Energy Minimize**, or **OpenMM Simulate**. It is especially useful for downloaded PDB entries, antibody complexes, multi-chain proteins, structures with missing terminal atoms, alternate-location conflicts, non-standard residues such as MSE, and inputs that fail force-field matching downstream.

Typical workflow: **Load PDB** → **OpenMM PDB Fixer** → **OpenMM ForceField Config** + **OpenMM Solvate** → **OpenMM Energy Minimize** → **OpenMM Simulate**. For ligand-containing systems, set `heterogen_mode` to `preserve`, add an **OpenMM Ligand Parameters** node for each ligand residue, and pass those ligand parameters into **OpenMM Solvate**, **OpenMM Energy Minimize**, and **OpenMM Simulate**. For protein-only simulations, use `remove_nonwater` to strip ligands/cofactors/metals, and usually keep `keep_water` disabled so **OpenMM Solvate** can add a fresh, consistent solvent box.

Best practice is to leave `replace_nonstandard_residues` enabled for crystal structures and use the default physiological `pH` of 7.4 unless your experiment requires a different protonation environment. Keep the default `heterogen_mode=error_if_present` when inspecting new structures, because it prevents accidental removal of a ligand or cofactor that may be scientifically important.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input PDB structure(s) to repair. Expects a dictionary-like PDB object where each entry maps a structure ID to PDB-format text containing parseable ATOM and/or HETATM records.</td><td style="word-wrap: break-word;">{'7KOO_antibody': 'HEADER    IMMUNE SYSTEM\nATOM      1  N   GLN H   1      12.414  23.810   8.921  1.00 42.18           N\n...'}</td></tr>
<tr><td style="word-wrap: break-word;">pH</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">pH used when adding hydrogens and assigning protonation states. Valid range is 0.0 to 14.0, with a default of 7.4 for physiological conditions.</td><td style="word-wrap: break-word;">7.4</td></tr>
<tr><td style="word-wrap: break-word;">heterogen_mode</td><td>False</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Controls handling of non-water HETATM residues such as ligands, cofactors, and metals. `error_if_present` stops when such residues are detected; `remove_nonwater` strips them; `preserve` keeps them for ligand-parameterized downstream workflows.</td><td style="word-wrap: break-word;">error_if_present</td></tr>
<tr><td style="word-wrap: break-word;">keep_water</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Keeps water molecules from the input PDB when `heterogen_mode` is `remove_nonwater`. Usually leave this disabled because **OpenMM Solvate** adds a fresh explicit water box.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">replace_nonstandard_residues</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Replaces non-standard residues with canonical equivalents where possible, such as MSE to MET or phosphorylated residues to their base amino acids. Enabled by default and recommended for most simulation preparation.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum wait time per PDB structure in seconds. Default is 600 seconds; valid range is 60 to 3600 seconds. Increase for very large complexes or unusual structures.</td><td style="word-wrap: break-word;">600</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Repaired PDB structure(s), keyed by the same PDB IDs as the input. The output is ready for **OpenMM Solvate**, **OpenMM Energy Minimize**, or **OpenMM Simulate**, depending on whether the structure already has the required simulation setup.</td><td style="word-wrap: break-word;">{'7KOO_antibody': 'HEADER    IMMUNE SYSTEM\nATOM      1  N   GLN H   1      12.414  23.810   8.921  1.00 42.18           N\nATOM      2  H   GLN H   1      12.027  24.632   8.476  1.00 42.18           H\n...\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">statistics</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">A table summarizing the fixed structures. Contains one row per input PDB with `pdb_id` and `num_atoms` columns.</td><td style="word-wrap: break-word;">[{'pdb_id': '7KOO_antibody', 'num_atoms': 6842}, {'pdb_id': '1HZH_fab', 'num_atoms': 6127}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Heterogen safety**: The default `error_if_present` mode intentionally stops when non-water HETATM residues are present, preventing accidental removal of ligands, cofactors, or metals.
- **Ligand workflows**: If you set `heterogen_mode` to `preserve`, downstream OpenMM nodes usually need matching **OpenMM Ligand Parameters** entries for each preserved ligand residue.
- **Water handling**: `keep_water` only matters when `heterogen_mode` is `remove_nonwater`. For most workflows, leave input waters removed and let **OpenMM Solvate** create a consistent solvent box.
- **Performance**: PDB fixing is usually fast for protein-only structures, but the timeout is applied per input PDB. Batch inputs can take up to `number of structures × timeout`.

## Troubleshooting
- **Input pdb dictionary is empty. At least one PDB structure is required.**: Connect a valid PDB-producing upstream node, such as **Load PDB**, and confirm the input contains PDB text rather than an empty object.
- **Structure contains non-standard residues that PDBFixer would strip**: Your structure contains ligands, cofactors, metals, or other non-water HETATM residues. Use `heterogen_mode=preserve` with **OpenMM Ligand Parameters**, or use `heterogen_mode=remove_nonwater` for a protein-only simulation.
- **PDBFixer received empty PDB content**: The upstream file input may have lost access to the uploaded file or produced no data. Re-upload the PDB file and rerun the workflow.
- **PDBFixer produced an empty topology**: The PDB content has no parseable ATOM/HETATM records or is not valid PDB text. Verify the file format and reload a valid structure.
