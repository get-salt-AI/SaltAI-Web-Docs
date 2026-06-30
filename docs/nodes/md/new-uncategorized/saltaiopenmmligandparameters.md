# OpenMM Ligand Parameters

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node registers chemistry information for one or more non-covalently bound ligands so OpenMM workflows can parameterize residues not covered by standard protein force fields. It accepts SDF, SMILES, or MOL2 ligand descriptions, associates them with a PDB residue code, and outputs a reusable ligand parameter list plus a summary table.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiopenmmligandparameters.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your input PDB contains HETATM ligand, cofactor, metal, or small-molecule residues that should be preserved during simulation. A typical workflow is: load or generate a PDB, run **OpenMM PDB Fixer** with `heterogen_mode=preserve`, create an **OpenMM ForceField Config**, then connect this node’s `ligand_params` output to the `ligands` input of **OpenMM Solvate**, **OpenMM Energy Minimize**, and/or **OpenMM Simulate**. For multiple ligands, chain several **OpenMM Ligand Parameters** nodes by connecting the previous node’s `ligand_params` output into `previous_ligands`; each ligand must use a unique residue code. Prefer SDF when available because it preserves bond orders, stereochemistry, and formal charge more reliably than SMILES. Use `openff-sage` for modern small-molecule parameterization, and choose `gaff-2.11` when reproducing legacy AMBER-family workflows or when OpenFF cannot parameterize a molecule.

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
<tr><td style="word-wrap: break-word;">residue_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The PDB residue code used by the ligand in the input structure. It is normalized to uppercase and must be 1–3 characters long. This must exactly match the residue name in the PDB HETATM records, such as `LIG`, `NAD`, or `HEM`.</td><td style="word-wrap: break-word;">NAD</td></tr>
<tr><td style="word-wrap: break-word;">sdf_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry as SDF text. This is the preferred format because it can carry explicit bond orders, stereochemistry, and formal charges. Provide this instead of `smiles` or `mol2_content` when an SDF representation is available.</td><td style="word-wrap: break-word;">SDF text for NAD with atom block, bond block, and charge information</td></tr>
<tr><td style="word-wrap: break-word;">smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry as a SMILES string. Use this as a fallback when SDF is unavailable. The protonation state, tautomer, charge, and stereochemistry should match the ligand coordinates in the input PDB.</td><td style="word-wrap: break-word;">CC(=O)NC1=CC=C(O)C=C1</td></tr>
<tr><td style="word-wrap: break-word;">mol2_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry as MOL2 text. This legacy format is supported, but SDF is generally preferred for robust ligand description.</td><td style="word-wrap: break-word;">MOL2 text for a heme cofactor including atom types, bonds, and charges</td></tr>
<tr><td style="word-wrap: break-word;">backend</td><td>False</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">Parameter generator backend. Valid options are `openff-sage` and `gaff-2.11`. `openff-sage` is the default modern small-molecule force field; `gaff-2.11` is useful for AMBER-compatible or legacy reproducibility workflows.</td><td style="word-wrap: break-word;">openff-sage</td></tr>
<tr><td style="word-wrap: break-word;">previous_ligands</td><td>False</td><td style="word-wrap: break-word;">OPENMM_LIGAND_PARAMS</td><td style="word-wrap: break-word;">An existing ligand parameter list from another OpenMM Ligand Parameters node. Use this to combine multiple ligand definitions into one list for downstream OpenMM nodes. Each chained ligand must have a unique `residue_name`.</td><td style="word-wrap: break-word;">[{"residue_name":"ATP","backend":"openff-sage","source":"sdf_content"}]</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_params</td><td style="word-wrap: break-word;">OPENMM_LIGAND_PARAMS</td><td style="word-wrap: break-word;">A list of ligand parameter definitions. Each entry contains the residue name, selected backend, and one ligand chemistry source. Connect this output to the `ligands` input on OpenMM Solvate, OpenMM Energy Minimize, or OpenMM Simulate.</td><td style="word-wrap: break-word;">[{"residue_name":"NAD","backend":"openff-sage","sdf_content":"SDF text for NAD","smiles":null,"mol2_content":null}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">A table summarizing the ligand definitions currently registered in the chain. Columns include `residue_name`, `backend`, and `source`; this is useful for confirming that each ligand residue will be handled downstream.</td><td style="word-wrap: break-word;">[{"residue_name":"NAD","backend":"openff-sage","source":"sdf_content"},{"residue_name":"MG","backend":"gaff-2.11","source":"mol2_content"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Ligand scope**: This node is intended for non-covalently bound ligands such as drugs in binding pockets, cofactors, and free ions. Covalently attached groups require a different workflow with custom residue templates and bond definitions.
- **Residue matching**: `residue_name` must match the PDB residue code exactly after uppercase normalization. If the PDB contains `HETATM ... NAD ...`, use `NAD`; if it contains `LIG`, use `LIG`.
- **Input chemistry**: Although the UI allows SDF, SMILES, and MOL2 fields, best practice is to provide exactly one chemistry source per ligand. SDF is preferred because it best preserves chemically important details.
- **Validation timing**: This node prepares the ligand definition and summary, but detailed chemistry parsing and parameterization issues may appear later when OpenMM Solvate, Energy Minimize, or Simulate consumes the ligand list.

## Troubleshooting
- **Duplicate ligand residue_name**: If you see an error about a duplicate residue name, check chained `previous_ligands` inputs and ensure every OpenMM Ligand Parameters node uses a unique PDB residue code.
- **residue_name must be 1-3 characters**: Use a valid PDB-style residue code such as `LIG`, `ATP`, `NAD`, or `MG`. Remove spaces and avoid long descriptive names.
- **Downstream template or parameterization failure**: Confirm the ligand residue name matches the PDB HETATM records, the PDB fixer preserved heterogens, and the provided SDF/SMILES/MOL2 represents the same protonation state and tautomer as the coordinates.
- **Unexpected missing ligand parameters downstream**: Make sure the `ligand_params` output is connected to the `ligands` input on every OpenMM node that handles the ligand-containing structure, especially Solvate before Minimize or Simulate.
