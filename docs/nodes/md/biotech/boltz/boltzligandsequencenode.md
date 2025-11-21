# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a ligand entry for Boltz YAML configurations. You choose whether to specify the ligand by SMILES or by a CCD code and assign it to one or more chain IDs. The node validates your choice and ensures only the appropriate field is set.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add a ligand to a Boltz modeling job. Select the input method (SMILES or CCD), provide the identifier, and set the chain ID(s). Feed the output into BoltzListCombinerNode and then into BoltzYAMLCombinerNode alongside protein/DNA/RNA sequences, constraints, and templates.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this ligand.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">["smiles", "ccd"]</td><td style="word-wrap: break-word;">How the ligand is specified. Choose 'smiles' to input a SMILES string or 'ccd' to input a CCD code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for the ligand. Required if ligand_type is 'smiles'.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">CCD (Chemical Component Dictionary) code for the ligand. Required if ligand_type is 'ccd'.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated additional chain IDs to duplicate this ligand across multiple chains. If provided, the 'id' field becomes a list.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single ligand mapping suitable for Boltz YAML assembly. Example structure: [{"ligand": {"id": "L" or ["L","M",...], "smiles": "..."}] or with "ccd" instead of "smiles".</td><td style="word-wrap: break-word;">[{"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Single specification rule**: Provide either a SMILES string or a CCD code based on ligand_type; not both.
- **Required fields by type**: If ligand_type='smiles', 'ligand_smiles' must be non-empty; if ligand_type='ccd', 'ligand_ccd' must be non-empty.
- **Multiple chains**: Supplying comma-separated IDs in 'multiple_chains' turns the 'id' field into a list to replicate the same ligand across those chains.
- **Downstream uniqueness**: Ensure chain IDs are unique across all sequences when later combined into a Boltz YAML.
- **Return shape**: Output is a list containing one ligand object; this is designed to feed directly into list-combining and YAML-combining nodes.

## Troubleshooting
- **Error: SMILES string is required when ligand_type is 'smiles'**: Enter a valid SMILES in 'ligand_smiles' or change ligand_type.
- **Error: CCD code is required when ligand_type is 'ccd'**: Enter a valid CCD code in 'ligand_ccd' or change ligand_type.
- **Error: Invalid ligand type**: Ensure 'ligand_type' is set to either 'smiles' or 'ccd'.
- **Duplicate chain ID warnings/errors downstream**: When assembling YAML, make sure no other sequence uses the same chain ID(s) as this ligand.
- **Multiple chains not applied**: Verify 'multiple_chains' is a comma-separated list without spaces-only entries (e.g., "M,N").
