# OpenMM Ligand Parameters

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node prepares a ligand-parameter list for small molecules, cofactors, or ions that are present as non-covalent residues in a structure. It packages ligand identity, source chemistry, and parameterization backend into a format consumed by downstream OpenMM Solvate, OpenMM Energy Minimize, and OpenMM Simulate nodes. It also supports chaining multiple ligands together while preventing duplicate residue codes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaiopenmmligandparameters.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when your input structure contains a ligand, cofactor, or other non-protein HETATM residue that standard biomolecular force fields will not parameterize automatically. Typical examples include a drug-like inhibitor in a binding pocket, NAD/FAD-like cofactors, heme-like small molecules, or free ions represented as separate residues.

In a typical workflow, this node sits alongside structure-preparation steps rather than directly modifying a PDB. A common pipeline is **Load PDB → OpenMM PDB Fixer → OpenMM Ligand Parameters → OpenMM Solvate → OpenMM Energy Minimize → OpenMM Simulate**. If you preserve heterogens in **OpenMM PDB Fixer**, connect the output of this node to the `ligands` input of downstream OpenMM nodes so those steps know how to parameterize the ligand residues.

This node works especially well with **OpenMM PDB Fixer** when `heterogen_mode` is set to `preserve`, and with **OpenMM ForceField Config** to keep force-field choices consistent across the workflow. If your system contains multiple distinct ligands, chain multiple **OpenMM Ligand Parameters** nodes using `previous_ligands`; each node adds one residue definition to the combined list.

Best practices: prefer `sdf_content` when available because it preserves bond orders, stereochemistry, and charge information more reliably than SMILES or MOL2. Ensure the `residue_name` exactly matches the residue code used in the structure, and make sure protonation and tautomer state match the actual PDB content. Use unique residue codes for each chained ligand entry.

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
<tr><td style="word-wrap: break-word;">residue_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The 1- to 3-character PDB residue code for the ligand. It must exactly match the residue name used in the input structure and is normalized to uppercase.</td><td style="word-wrap: break-word;">NAD</td></tr>
<tr><td style="word-wrap: break-word;">sdf_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry provided as SDF text. This is the preferred format because it typically preserves explicit bond orders, stereochemistry, and formal charges more accurately than other inputs. Provide exactly one chemistry source when possible.</td><td style="word-wrap: break-word;">NAD cofactor SDF block exported from a docking or cheminformatics tool</td></tr>
<tr><td style="word-wrap: break-word;">smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry as a SMILES string. Use this when SDF is not available. The protonation state and tautomer should match the ligand represented in the structure.</td><td style="word-wrap: break-word;">CC(=O)NC1=CC=C(C=C1)O</td></tr>
<tr><td style="word-wrap: break-word;">mol2_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand chemistry as MOL2 text. Supported as a legacy alternative, but generally less preferred than SDF for modern workflows.</td><td style="word-wrap: break-word;">MOL2 record for a kinase inhibitor exported from a molecular modeling package</td></tr>
<tr><td style="word-wrap: break-word;">backend</td><td>False</td><td style="word-wrap: break-word;">["openff-sage", "gaff-2.11"]</td><td style="word-wrap: break-word;">Selects the small-molecule parameterization backend. `openff-sage` is the default modern option for many small molecules, while `gaff-2.11` is useful for legacy AMBER-style reproducibility or fallback cases.</td><td style="word-wrap: break-word;">openff-sage</td></tr>
<tr><td style="word-wrap: break-word;">previous_ligands</td><td>False</td><td style="word-wrap: break-word;">OPENMM_LIGAND_PARAMS</td><td style="word-wrap: break-word;">An existing ligand-parameter list from another OpenMM Ligand Parameters node. Use it to chain multiple unique ligands into a single combined list for one simulation system.</td><td style="word-wrap: break-word;">A ligand parameter list already containing ATP, to which a second entry for MG will be added</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_params</td><td style="word-wrap: break-word;">OPENMM_LIGAND_PARAMS</td><td style="word-wrap: break-word;">A list of ligand parameter definitions for downstream OpenMM workflow nodes. Each entry includes the residue name, selected backend, and one of the supplied chemistry sources, allowing Solvate, Energy Minimize, and Simulate to parameterize the matching ligand residue.</td><td style="word-wrap: break-word;">[{"residue_name":"NAD","backend":"openff-sage","sdf_content":"<sdf text>","smiles":null,"mol2_content":null},{"residue_name":"MG","backend":"gaff-2.11","sdf_content":null,"smiles":"[Mg+2]","mol2_content":null}]</td></tr>
<tr><td style="word-wrap: break-word;">summary</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">A table summarizing the currently registered ligand entries, including residue name, backend, and which chemistry source was provided. It is useful for checking that all expected ligands were chained correctly before simulation.</td><td style="word-wrap: break-word;">A table with rows like: residue_name=NAD, backend=openff-sage, source=sdf_content; residue_name=MG, backend=gaff-2.11, source=smiles</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Behavior**: This node does not itself validate or parameterize the chemistry; it prepares ligand definitions for downstream OpenMM nodes, where chemistry parsing and parameter generation are applied.
- **Limitations**: It is intended for non-covalent ligands only. Covalently attached payloads, covalent inhibitors, crosslinks, or custom bonded residue modifications are outside this node's scope.
- **Input matching**: `residue_name` must exactly match the residue code in the structure file. A mismatch means downstream nodes will not associate the provided chemistry with the ligand in the PDB.
- **Chaining**: Each chained ligand entry must use a unique residue code. Duplicate `residue_name` values are rejected to avoid ambiguous parameter assignment.

## Troubleshooting
- **Duplicate ligand residue_name**: If you see an error about a duplicate residue name, make sure each chained OpenMM Ligand Parameters node uses a different `residue_name`, such as `NAD` and `MG` rather than repeating `NAD`.
- **Residue not being parameterized downstream**: If Solvate, Minimize, or Simulate behaves as if the ligand is missing parameters, verify that `residue_name` exactly matches the PDB residue code and that the ligand was preserved upstream in OpenMM PDB Fixer.
- **Ligand chemistry fails later in the workflow**: If downstream processing rejects the ligand, prefer `sdf_content` over SMILES or MOL2, and ensure the supplied chemistry matches the protonation and tautomer state of the structure actually present in the PDB.
