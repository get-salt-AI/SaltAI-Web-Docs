# OpenMM PDB Fixer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node cleans and repairs input PDB structure data so it can be used reliably in downstream OpenMM solvation, minimization, and simulation steps. It adds a controlled preprocessing step for real-world structures with missing atoms, non-standard residues, alternate locations, and protonation-state issues, while giving explicit control over how ligands, cofactors, metals, and waters are handled.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiopenmmpdbfixer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a molecular simulation workflow whenever your starting structure comes from an experimental PDB file or any source that may contain incomplete or non-standard topology. It is especially useful for multi-chain proteins, antibodies, structures with chain breaks, missing terminal atoms, alternate conformations, or modified residues such as MSE that often cause downstream force-field setup to fail.

Typical workflow position: connect a structure-producing node such as a PDB loader into **OpenMM PDB Fixer**, then send its `fixed.pdb` output into **OpenMM Solvate**. From there, the usual sequence is **OpenMM ForceField Config** → **OpenMM Solvate** → **OpenMM Energy Minimize** → **OpenMM Simulate**. If your structure contains ligands or other non-water heterogens that must be preserved, pair this node with **OpenMM Ligand Parameters** and use `heterogen_mode` set to `preserve` so downstream OpenMM nodes can parameterize those residues correctly.

Best practices: keep the default `error_if_present` mode unless you are certain you want to discard heterogens or you already have ligand parameterization prepared. Use `replace_nonstandard_residues=true` for most crystal structures to improve compatibility with standard protein force fields. Leave `keep_water=false` in most cases, because explicit solvent is usually added later by **OpenMM Solvate** and input crystallographic waters are not typically needed for general-purpose simulation pipelines.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more PDB structures to repair. The node expects a PDB collection keyed by structure ID, and processes each entry individually.</td><td style="word-wrap: break-word;">{"7KVE":"HEADER ANTIBODY COMPLEX ...\nATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">pH</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">pH value used when assigning protonation states during hydrogen addition. Valid range is 0.0 to 14.0; physiological workflows typically use 7.4.</td><td style="word-wrap: break-word;">7.4</td></tr>
<tr><td style="word-wrap: break-word;">heterogen_mode</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Controls treatment of non-water HETATM residues. `error_if_present` stops execution if ligands, cofactors, or metals are present; `remove_nonwater` strips non-water heterogens; `preserve` keeps them for downstream ligand-aware workflows.</td><td style="word-wrap: break-word;">preserve</td></tr>
<tr><td style="word-wrap: break-word;">keep_water</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to retain water molecules from the input structure. This setting only matters when `heterogen_mode` is `remove_nonwater`. In most explicit-solvent workflows, input waters are not needed because solvent is added later.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">replace_nonstandard_residues</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to convert non-standard residues to their canonical equivalents where possible, such as MSE to MET. This improves compatibility with standard force fields.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum wait time in seconds for repairing each input PDB. Valid range is 60 to 3600 seconds; applied per structure in a batch.</td><td style="word-wrap: break-word;">600</td></tr>
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
<tr><td style="word-wrap: break-word;">fixed.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Repaired PDB structure collection ready for downstream solvation, minimization, or simulation. Each output entry contains cleaned coordinates and topology text for the corresponding input structure.</td><td style="word-wrap: break-word;">{"7KVE":"HEADER REPAIRED STRUCTURE ...\nATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">statistics</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Tabular summary of repaired structures with one row per input PDB. Includes at least `pdb_id` and `num_atoms` so downstream steps can verify successful repair and structure size.</td><td style="word-wrap: break-word;">[{"pdb_id":"7KVE","num_atoms":18432}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Behavior**: The default `heterogen_mode` is `error_if_present`, which intentionally prevents silent removal of ligands, cofactors, or metals. This protects simulation workflows from accidentally discarding chemically important molecules.
- **Integration**: If you choose `preserve`, you should typically use **OpenMM Ligand Parameters** before **OpenMM Solvate**, **OpenMM Energy Minimize**, or **OpenMM Simulate** so preserved ligand residues can be parameterized downstream.
- **Workflow**: `keep_water` only affects the `remove_nonwater` path. It does not change behavior when heterogens are preserved or when execution stops on heterogen detection.
- **Performance**: Repair is generally fast for protein-only structures, but batch inputs are processed per PDB and the timeout applies to each one individually, so large batches can take proportionally longer.

## Troubleshooting
- **Force-field setup fails later with template or atom-mismatch errors**: Run the structure through this node before solvation or minimization, and keep `replace_nonstandard_residues` enabled so modified residues are normalized where possible.
- **Node stops because heterogens are present**: This is expected when `heterogen_mode` is `error_if_present`. Switch to `preserve` if the ligand or cofactor must remain, then provide matching residue definitions using **OpenMM Ligand Parameters**; or use `remove_nonwater` for protein-only simulations.
- **Unexpected waters remain or disappear**: Check the combination of `heterogen_mode` and `keep_water`. `keep_water` only applies when using `remove_nonwater`, and most workflows should still add a fresh solvent box later with **OpenMM Solvate**.
- **Repair times out on a large or unusual structure**: Increase the `timeout` value, especially for very large complexes or problematic input files processed in batches.
