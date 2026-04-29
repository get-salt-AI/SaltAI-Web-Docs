# Boltz Ligand Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node builds a ligand specification object formatted for Boltz YAML workflows. You choose whether to define the ligand by a SMILES string or by a PDB CCD code and assign one or more chain IDs to represent identical copies of the same ligand. The output is a ligand sequence dictionary that can be combined with protein/DNA/RNA sequences and passed into Boltz YAML assembly and prediction nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzligandsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to include a small-molecule ligand in a Boltz structure prediction or affinity workflow. Typical usage: (1) decide whether your ligand is best specified by a SMILES string (for arbitrary or designed molecules) or by a CCD code (for standard PDB ligands), (2) assign a primary chain ID (commonly "L" for a single ligand), and optionally specify additional chain IDs if you want multiple identical ligand copies (e.g., L,M,N). Upstream, this node is usually configured manually or fed by data management nodes that provide SMILES/CCD identifiers. Downstream, connect its output to a "Boltz List Combiner" node along with protein sequences (from "Boltz Protein Sequence" or "Boltz Sequence Builder") and any constraints/templates/properties, then into "Boltz YAML Combiner" and finally "Boltz Predict". When working with affinity prediction (via "Boltz Property Builder" and "Boltz Predict"), ensure that at least one ligand from this node is present in your sequences. Best practice: keep chain IDs unique across all entities in the YAML and align ligand chain IDs with any constraints or properties that refer to the ligand.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for the ligand in the Boltz complex. Must be unique across all chains in the YAML configuration. Single uppercase letters are typical (e.g., L), but any non-empty string is allowed as long as it does not collide with other chain IDs.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">ligand_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE["smiles","ccd"]</td><td style="word-wrap: break-word;">Specifies how the ligand is defined. Use "smiles" when you have a SMILES string describing the ligand structure. Use "ccd" when you want to refer to a standard PDB ligand by its Chemical Component Dictionary (CCD) code. Determines which optional input (ligand_smiles or ligand_ccd) is required.</td><td style="word-wrap: break-word;">smiles</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string representation of the ligand, used only when ligand_type is "smiles". Must be non-empty if ligand_type="smiles"; ignored otherwise. Should be a valid SMILES describing the small molecule structure.</td><td style="word-wrap: break-word;">CC1=CC(=O)NC(=O)N1CC2=CC=CC=C2</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDB Chemical Component Dictionary (CCD) code for the ligand, used only when ligand_type is "ccd". Must be non-empty if ligand_type="ccd"; ignored otherwise. Typically a 3-letter code corresponding to a standard ligand in the PDB.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of this ligand. Each extra ID will share the same ligand definition (same SMILES or CCD). All chain IDs (primary plus these) must be unique across the entire Boltz YAML. Whitespace around commas is ignored.</td><td style="word-wrap: break-word;">M,N</td></tr>
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
<tr><td style="word-wrap: break-word;">ligand_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single-element list containing one ligand descriptor dictionary formatted for Boltz YAML. The structure is: [{"ligand": {"id": <chain_id or [chain_ids]>, "smiles": <string>}}] when ligand_type="smiles", or [{"ligand": {"id": <chain_id or [chain_ids]>, "ccd": <string>}}] when ligand_type="ccd". If multiple_chains is set, id is a list of chain IDs; otherwise it is a single chain ID string.</td><td style="word-wrap: break-word;">[{"ligand": {"id": ["L","M"], "smiles": "CC1=CC(=O)NC(=O)N1CC2=CC=CC=C2"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Validation of ligand definition**: The node strictly enforces that the ligand data matches ligand_type: when ligand_type is "smiles" it requires a non-empty ligand_smiles; when "ccd" it requires a non-empty ligand_ccd, and other fields are ignored for that run.
- **Multiple copies handling**: When multiple_chains is provided, the id field becomes a list of chain IDs, and all copies refer to the same SMILES or CCD; Boltz treats them as identical ligands in different chains.
- **Chain ID uniqueness**: This node does not check against other sequences, but Boltz YAML Combiner later enforces global uniqueness of chain IDs. Reusing a chain ID across different entities will cause the combiner to fail.
- **Integration with other Boltz nodes**: The output is meant to be merged with other sequence, constraint, template, and property objects using "Boltz List Combiner" and "Boltz YAML Combiner" before running "Boltz Predict".

## Troubleshooting
- **SMILES string is required when ligand_type is 'smiles'**: Ensure ligand_type is set to "smiles" only when ligand_smiles is non-empty. If you intended to use a CCD code instead, switch ligand_type to "ccd" and fill ligand_ccd.
- **CCD code is required when ligand_type is 'ccd'**: You selected ligand_type="ccd" but did not provide a CCD code. Enter a valid 3-letter PDB CCD code (for example ATP or HEM) or change ligand_type to "smiles" and use a SMILES string.
- **Invalid ligand type error**: This occurs if ligand_type is set to a value other than "smiles" or "ccd" (for example from manual JSON editing). Reset ligand_type to one of the supported options.
- **Duplicate chain ID found during YAML combination**: This error appears in Boltz YAML Combiner, not here, but is usually caused by giving a ligand chain ID (or entries in multiple_chains) that collide with protein or other ligand chains. Adjust chain_id and multiple_chains so each chain label across all sequences is unique.
