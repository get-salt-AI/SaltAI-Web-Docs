# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a ligand entry for Boltz YAML workflows. The ligand can be defined either by a SMILES string or a PDB CCD code and assigned a unique chain ID (or multiple IDs for identical copies). The node outputs a list containing one ligand specification ready to be merged into a full Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add a small-molecule ligand to a Boltz modeling job. Select how to describe the ligand (SMILES or CCD), set its primary chain ID, and optionally specify additional chain IDs for identical ligand copies. Connect the output to a list combiner and then to the Boltz YAML combiner, together with protein sequences, constraints, templates, and optional property specifications such as affinity.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for the ligand. This must be unique within the overall Boltz configuration; if multiple_chains is used, this is the first ID in the series.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">["smiles", "ccd"]</td><td style="word-wrap: break-word;">Selects how the ligand is specified. Use "smiles" to provide a SMILES string, or "ccd" to provide a PDB Chemical Component Dictionary (CCD) code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string describing the ligand. This field becomes required when ligand_type is set to "smiles". Leading and trailing whitespace is removed.</td><td style="word-wrap: break-word;">CCO</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDB CCD code describing the ligand. This field becomes required when ligand_type is set to "ccd". Leading and trailing whitespace is removed.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs for creating identical copies of the same ligand. When provided, the resulting ligand "id" will be a list of chain IDs including chain_id and all additional IDs.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one ligand specification in Boltz format. The single list element is a mapping under the key "ligand" that includes an "id" (string or list of chain IDs) and either a "smiles" or "ccd" field according to ligand_type.</td><td style="word-wrap: break-word;">[{"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input pairing rule**: When ligand_type is "smiles", a non-empty ligand_smiles value is required; when ligand_type is "ccd", a non-empty ligand_ccd value is required.
- **Chain ID uniqueness**: All chain IDs (from chain_id and multiple_chains) must be unique across every sequence and ligand in the final Boltz YAML configuration to avoid validation errors downstream.
- **Multiple copies behavior**: Providing multiple_chains (for example, "M,N") causes the node to output the ligand "id" as a list of chain IDs, representing identical ligand copies.
- **Whitespace handling**: Leading and trailing whitespace is stripped from SMILES and CCD inputs, which helps avoid subtle formatting issues.
- **No chemical validation**: The node does not check that the SMILES is chemically valid or that the CCD code exists; it only ensures that required fields are present and formatted.

## Troubleshooting
- **SMILES-required error when ligand_type = "smiles"**: Ensure ligand_smiles is provided and non-empty, or change ligand_type to "ccd" if you intend to use a CCD code.
- **CCD-required error when ligand_type = "ccd"**: Ensure ligand_ccd is provided and non-empty, or change ligand_type to "smiles" if you intend to use a SMILES string.
- **Duplicate chain ID error in downstream nodes**: Check that chain_id and any IDs in multiple_chains do not overlap with chain IDs used for other sequences or ligands in the same workflow.
- **Unexpected list wrapping of output**: Remember that the node outputs a list containing one ligand mapping. If you are combining several sequence or ligand outputs, pass them through a list-combining node before feeding them to the Boltz YAML combiner.
