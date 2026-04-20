# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node creates ligand sequence objects in the Boltz YAML format, specifying ligands either by SMILES string or by CCD (PDB chemical component) code. It manages ligand chain IDs, supports multiple identical ligand copies, and enforces that each ligand is defined consistently with the selected ligand type. The output is a compact ligand description ready to be combined with other Boltz components such as protein sequences, constraints, templates, and properties.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to define ligands for Boltz-based protein–ligand or multi-component molecular modeling workflows. A typical pattern is to generate protein sequences with "Boltz Protein Sequence" or "Boltz Sequence Builder", define one or more ligands with "Boltz Ligand Sequence", then merge all components with "Boltz List Combiner" and feed them into "Boltz YAML Combiner" before running "Boltz Predict". Choose `ligand_type = "smiles"` when you have arbitrary or designed small molecules defined by SMILES (for example, from a cheminformatics pipeline), or `ligand_type = "ccd"` when you are using a standard PDB chemical component. Set the `chain_id` to a unique identifier (such as L) and ensure it matches any binder references used later in "Boltz Constraint Builder" (e.g., pocket constraints) or "Boltz Property Builder" (e.g., affinity for a specific binder chain). Use the `multiple_chains` field if you need multiple identical copies of the same ligand, such as two copies of the same inhibitor bound to different symmetric sites, and then reference those chain IDs consistently in downstream constraints.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this ligand in the Boltz YAML. This is typically a single uppercase letter (for example, L) but may be any short unique string. It must be globally unique among all sequence IDs in the same Boltz YAML and is used to reference this ligand in constraints and properties.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">["smiles", "ccd"]</td><td style="word-wrap: break-word;">Determines how the ligand is specified. "smiles" means the ligand is described by a SMILES string via `ligand_smiles`; "ccd" means the ligand is described by a PDB chemical component code via `ligand_ccd`. The node validates that the appropriate field is provided for the chosen type.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES representation of the ligand, used when `ligand_type` is "smiles". Must be a non-empty, syntactically valid SMILES string; otherwise the node raises an error if `ligand_type` is "smiles". This field is ignored when `ligand_type` is "ccd".</td><td style="word-wrap: break-word;">CC1=CC(=O)NC(=O)N1CCC2=CN=CC=C2</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDB chemical component (CCD) code for the ligand, used when `ligand_type` is "ccd". Must be a non-empty string such as ATP, HEM, or NAG; otherwise the node raises an error if `ligand_type` is "ccd". This field is ignored when `ligand_type` is "smiles".</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs for identical copies of the same ligand (for example, M,N). When provided, the output ligand `id` becomes a list including the primary `chain_id` plus these extra IDs. All chain IDs must remain unique across the whole Boltz YAML to avoid validation errors later.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single-element list containing one ligand specification dictionary in Boltz sequence format. The outer list enables composition with other sequence objects via Boltz List Combiner. Inside the list, the object has a "ligand" key with fields: `id` (string or list of chain IDs) and either `smiles` or `ccd`, depending on `ligand_type`.</td><td style="word-wrap: break-word;">[{'ligand': {'id': ['L', 'M'], 'smiles': 'CC1=CC(=O)NC(=O)N1CCC2=CN=CC=C2'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Type-dependent requirements**: When `ligand_type` is "smiles", `ligand_smiles` must be non-empty; when it is "ccd", `ligand_ccd` must be non-empty. The node raises a clear error if the required field for the chosen type is missing.
- **Chain ID uniqueness**: The `id` field in the output (either a single chain ID or a list when `multiple_chains` is used) must remain unique across all sequences. Duplicate chain IDs will be caught downstream by Boltz YAML validation and will prevent prediction.
- **Multiple copies semantics**: Setting `multiple_chains` creates identical ligand copies sharing the same structural description. Downstream, each chain ID can be referenced independently in constraints or properties while still referring to the same chemical entity.
- **No deep chemistry validation**: The node only checks that SMILES and CCD inputs are non-empty and correctly routed by `ligand_type`; it does not verify chemical validity or existence in external databases. Invalid SMILES or unknown CCD codes may fail later during Boltz prediction or external processing.

## Troubleshooting
- **Error: 'SMILES string is required when ligand_type is 'smiles''**: Verify that `ligand_type` is set to "smiles" only when `ligand_smiles` has a non-empty value. If you intend to use a CCD code instead, change `ligand_type` to "ccd" and fill `ligand_ccd`.
- **Error: 'CCD code is required when ligand_type is 'ccd''**: Ensure `ligand_ccd` contains a valid, non-empty chemical component code when `ligand_type` is "ccd". If you meant to use a SMILES string, switch `ligand_type` to "smiles" and provide `ligand_smiles` instead.
- **Downstream message: 'Duplicate chain ID found' in YAML combiner or prediction**: Check all sequence-building nodes (Boltz Protein Sequence, Boltz Sequence Builder, and Boltz Ligand Sequence) to ensure that `chain_id` and all IDs in `multiple_chains` are unique across the full workflow.
- **Boltz prediction fails or produces invalid ligand geometry**: Confirm that your SMILES string is chemically valid using a cheminformatics toolkit, or that your CCD code corresponds to a known chemical component. If necessary, simplify or correct the ligand representation before re-running.
