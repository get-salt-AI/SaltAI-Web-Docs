# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a single molecular sequence entry formatted for Boltz YAML. Supports protein, DNA, RNA, and ligand entities, including chain assignment, optional multiple identical chains, modifications, and polymer-specific options like MSA and cyclic polymers. Returns a list containing one sequence mapping ready to be combined into a full Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define each chain/entity in a Boltz job. For proteins/DNA/RNA, provide a sequence (plain text or FASTA). For ligands, specify either a SMILES string or a CCD code. If you need identical copies of the same chain, list additional chain IDs. Connect outputs to a list combiner and then to the Boltz YAML combiner for prediction.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of molecular entity to define. Determines which other inputs are required and how the sequence is interpreted.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity. Must be unique across all chains in the final YAML.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino acid or nucleotide sequence for protein/DNA/RNA. Can be plain sequence characters or FASTA-formatted text. If FASTA-formatted, the first header is used to name the sequence and the content after headers is extracted.</td><td style="word-wrap: break-word;">MKTAYIAKQRQISFVKSHFSRQ</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence in FASTA format for protein/DNA/RNA as an alternative to 'sequence'. If provided, it takes precedence and the header is used to name the sequence.</td><td style="word-wrap: break-word;">>my_protein MKTAYIAKQRQISFVKSHFSRQ</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Multiple Sequence Alignment for proteins in A3M or CSV format. Leave empty to use an 'empty' MSA.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAK- >seq2 MKTAFIAKQ</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for ligand entities. Provide this OR 'ligand_ccd' (not both).</td><td style="word-wrap: break-word;">CCO</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Three-letter CCD code for ligand entities. Provide this OR 'ligand_smiles' (not both).</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications for polymers. One per line in the format 'position:ccd_code'.</td><td style="word-wrap: break-word;">5:MSE 23:SEP</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the polymer as cyclic when true.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of this entity.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single Boltz-ready sequence mapping for the specified entity. Designed to be merged with other sequences using a list combiner before building the final YAML.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MKTAYIAK...", "_sequence_name": "my_protein", "msa": "empty"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **For polymers (protein/dna/rna), a sequence is required**: provide either 'sequence' (plain or FASTA text) or 'sequence_fasta'.
- **For ligands, exactly one identifier is required**: provide 'ligand_smiles' or 'ligand_ccd', but not both.
- **Multiple chains**: Use 'multiple_chains' to generate identical copies; ensure all chain IDs are unique across the final YAML.
- **MSA handling (proteins only)**: Accepts A3M or CSV. If omitted, an 'empty' MSA will be used downstream.
- **Sequence names**: When a FASTA header is present, the first header token (commas removed) is used as the sequence name.
- **Modifications**: Use integer residue positions with CCD codes in 'position:ccd' format, one per line.
- **Cyclic polymers**: Enable 'cyclic' to mark the chain as cyclic in the YAML.

## Troubleshooting
- **Error: 'Sequence is required for protein/dna/rna'**: Provide a valid sequence via 'sequence' or 'sequence_fasta'. If using FASTA, ensure it starts with '>' and contains sequence lines.
- **Error: 'SMILES or CCD code is required for ligands'**: For ligand entities, set either 'ligand_smiles' or 'ligand_ccd'.
- **Error: 'Provide either SMILES or CCD code, not both'**: Remove one of the two ligand identifiers.
- **Invalid modification format warning**: Ensure each line is 'position:ccd_code' with an integer position (e.g., '23:SEP').
- **Unexpected characters in FASTA name**: Commas are stripped from FASTA headers. Consider simplifying header tokens.
- **Duplicate chain IDs when combining YAML**: If later nodes report duplicate chain IDs, revise 'chain_id' and 'multiple_chains' to ensure global uniqueness.
