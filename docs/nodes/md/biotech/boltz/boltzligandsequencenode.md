# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a ligand entry for Boltz YAML configurations. Supports specifying the ligand either by a SMILES string or a PDB CCD (chemical component dictionary) code. Can assign the ligand to one or multiple chain IDs to represent identical ligand copies in a complex.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when assembling the sequence/specification list for a Boltz run that includes small-molecule ligands. Choose whether the ligand is defined by a SMILES string or a CCD code, provide the appropriate value, and set the chain ID(s) the ligand should occupy. Feed its output into Boltz list or YAML combiner nodes before prediction or diffusion steps.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for the ligand (e.g., L). This is used as the ligand chain in the final configuration.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">['smiles', 'ccd']</td><td style="word-wrap: break-word;">Select how the ligand will be specified: 'smiles' for a SMILES string or 'ccd' for a PDB chemical component code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for the ligand. Required when ligand_type is 'smiles'.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDB CCD (3-letter) chemical component code for the ligand. Required when ligand_type is 'ccd'.</td><td style="word-wrap: break-word;">HEM</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of the ligand (e.g., M,N). When provided, the ligand ID becomes a list of chains.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one ligand specification dictionary compatible with Boltz YAML assembly. The object encodes the ligand ID(s) and either a SMILES or CCD field.</td><td style="word-wrap: break-word;">[{"ligand": {"id": ["L", "M"], "smiles": "<smiles-string>"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input coupling**: If ligand_type is 'smiles', you must provide a non-empty 'ligand_smiles'. If ligand_type is 'ccd', you must provide a non-empty 'ligand_ccd'.
- **Multiple chains behavior**: When 'multiple_chains' is provided, the ligand 'id' becomes a list of chain IDs; otherwise it is a single string.
- **Output shape**: The node returns a list with a single ligand entry, intended to be merged with other sequence entries using Boltz list/YAML combiner nodes.
- **Validation**: The node will raise an error if the required ligand value for the selected type is missing or if an unsupported ligand_type is provided.

## Troubleshooting
- **Error: 'SMILES string is required when ligand_type is "smiles"'**: Set 'ligand_type' to 'smiles' and provide a valid non-empty 'ligand_smiles' value.
- **Error: 'CCD code is required when ligand_type is "ccd"'**: Set 'ligand_type' to 'ccd' and provide a valid non-empty 'ligand_ccd' value.
- **Error: 'Invalid ligand type'**: Ensure 'ligand_type' is exactly either 'smiles' or 'ccd'.
- **Ligand not recognized downstream**: Verify that the chain_id(s) match the rest of your configuration and that the output is passed into the appropriate Boltz list or YAML combiner before prediction.
