# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a ligand entry for Boltz YAML. You choose how to specify the ligand (SMILES string or CCD code) and assign one or multiple chain IDs. The node validates inputs and outputs a standardized ligand sequence object ready to be combined into a full Boltz configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add a ligand definition to a Boltz run. Select the ligand specification type (SMILES or CCD), provide the corresponding value, and set one or more chain IDs. Feed its output into a list combiner and then into the Boltz YAML combiner alongside proteins, constraints, templates, and properties.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain ID to assign to this ligand.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">ENUM['smiles','ccd']</td><td style="word-wrap: break-word;">How the ligand is specified: 'smiles' to provide a SMILES string, or 'ccd' to provide a PDB CCD code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand SMILES string. Required when ligand_type is 'smiles'.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand CCD (Chemical Component Dictionary) three-letter code. Required when ligand_type is 'ccd'.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs if you want multiple identical ligand copies.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one ligand sequence object in Boltz format. Example structure: [{'ligand': {'id': 'L' or ['L','M',...], 'smiles': '...' OR 'ccd': '...'}}].</td><td style="word-wrap: break-word;">[{'ligand': {'id': ['L','M'], 'smiles': 'CC(=O)Oc1ccccc1C(=O)O'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- You must provide exactly one ligand specification matching ligand_type: 'smiles' requires ligand_smiles; 'ccd' requires ligand_ccd.
- If ligand_type is 'smiles' and ligand_smiles is empty, or ligand_type is 'ccd' and ligand_ccd is empty, the node will raise an error.
- chain_id defaults to 'L'. Use multiple_chains to duplicate the same ligand across additional chains (e.g., 'M,N').
- The output is a list containing a single mapping; it is designed to be combined with other sequences via a list combiner and then assembled into YAML with the Boltz YAML combiner.

## Troubleshooting
- Error: "SMILES string is required when ligand_type is 'smiles'" — Provide a non-empty ligand_smiles value or switch ligand_type to 'ccd' with a CCD code.
- Error: "CCD code is required when ligand_type is 'ccd'" — Provide a non-empty ligand_ccd value or switch ligand_type to 'smiles' with a SMILES string.
- Error: "Invalid ligand type: <value>" — Set ligand_type to either 'smiles' or 'ccd'.
- Unexpected result format downstream — Ensure you pass this node's output through the List Combiner and then into the YAML Combiner to create a valid Boltz YAML and files set.
