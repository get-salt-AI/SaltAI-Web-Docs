# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a ligand entry for Boltz YAML workflows. You can define the ligand either by a SMILES string or by a CCD (Chemical Components Dictionary) code and assign it to one or more chain identifiers.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to add a small-molecule ligand to a Boltz configuration. Create one ligand specification per unique ligand and chain set, then combine the resulting outputs with other sequence and configuration nodes (for example a YAML combiner) before running prediction or diffusion steps.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for the ligand. This should be unique within the structure and typically uses a single-letter ID.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">How the ligand is specified. Choose 'smiles' to provide a SMILES string, or 'ccd' to provide a PDB CCD code.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for the ligand. Required if ligand_type is 'smiles'.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chemical Components Dictionary (CCD) three-letter code for the ligand. Required if ligand_type is 'ccd'.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of this ligand across multiple chains.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A ligand sequence payload suitable for inclusion in a Boltz YAML configuration. It contains the ligand entity with its chain id(s) and either a SMILES or CCD specification.</td><td style="word-wrap: break-word;">[{"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Ligand specification is mutually exclusive**: provide either 'ligand_smiles' when ligand_type is 'smiles' or 'ligand_ccd' when ligand_type is 'ccd'.
- **Multiple chains**: Use 'multiple_chains' to replicate the same ligand across several chain IDs (e.g., 'M,N'). The node will package all specified chain IDs into a single ligand entry.
- **Output is a collection**: The output is a collection containing the ligand object, ready to be merged with other sequences and configuration elements in a Boltz YAML.
- **Validation**: The node will raise an error if the required field for the chosen ligand_type is missing or empty.

## Troubleshooting
- **Error: 'SMILES string is required when ligand_type is "smiles"'**: Set 'ligand_smiles' to a non-empty, valid SMILES string or switch 'ligand_type' to 'ccd' and provide 'ligand_ccd'.
- **Error: 'CCD code is required when ligand_type is "ccd"'**: Provide a valid three-letter CCD code in 'ligand_ccd' or switch 'ligand_type' to 'smiles' and provide 'ligand_smiles'.
- **Unexpected or conflicting chain IDs**: Ensure 'chain_id' is set and any 'multiple_chains' values are comma-separated without spaces or duplicates.
- **Downstream YAML merge issues**: Confirm that this output is combined with protein sequences, constraints, templates, or properties using the appropriate Boltz combiner node before prediction.
