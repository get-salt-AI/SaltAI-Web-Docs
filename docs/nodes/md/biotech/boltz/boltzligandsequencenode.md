# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a ligand entry for Boltz YAML workflows. You can define the ligand by either a SMILES string or a PDB CCD code and assign it a unique chain ID (or multiple IDs for identical copies). The node outputs a list containing one ligand specification ready to be combined into a Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add a small-molecule ligand to a Boltz modeling job. Choose how to specify the ligand (SMILES or CCD), set the ligand's chain ID, and (optionally) duplicate it across multiple chains. Connect its output to a list combiner and then to the Boltz YAML combiner, along with protein sequences, constraints, templates, and (optionally) properties such as affinity.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unique chain identifier for the ligand. If you later duplicate the ligand, this is the first chain in the series.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">["smiles", "ccd"]</td><td style="word-wrap: break-word;">How the ligand will be specified. Choose 'smiles' to provide a SMILES string, or 'ccd' to provide a PDB CCD code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string to define the ligand. Required when ligand_type is 'smiles'.</td><td style="word-wrap: break-word;">CCO</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDB CCD code to define the ligand. Required when ligand_type is 'ccd'.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of the ligand. If provided, the 'id' will be a list of chain IDs.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one ligand specification in Boltz format. The element is a mapping with key 'ligand' holding 'id' and either 'smiles' or 'ccd'.</td><td style="word-wrap: break-word;">[{"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input pairing rule**: When ligand_type is 'smiles', ligand_smiles is required; when 'ccd', ligand_ccd is required.
- **Uniqueness**: Chain IDs must be unique across all sequences in the final YAML. Duplicates will cause validation errors in the YAML combiner.
- **Multiple copies**: Use multiple_chains (e.g., "M,N") to create identical ligand copies; the node will output 'id' as a list when multiple chains are provided.
- **Whitespace handling**: Leading/trailing spaces are stripped from SMILES and CCD inputs.
- **No chemistry validation**: This node does not validate chemical correctness of the SMILES or the existence of the CCD code; it only enforces presence and format.

## Troubleshooting
- **Error: 'SMILES string is required when ligand_type is 'smiles''**: Provide a non-empty ligand_smiles value or switch ligand_type to 'ccd'.
- **Error: 'CCD code is required when ligand_type is 'ccd''**: Provide a non-empty ligand_ccd value or switch ligand_type to 'smiles'.
- **Duplicate chain ID error downstream**: Ensure the chain_id (and any in multiple_chains) do not collide with other sequences' IDs when combined in the YAML.
- **Unexpected output structure**: This node returns a list containing one ligand mapping. If combining with others, pass through a list combiner before the YAML combiner.
