# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a single Boltz YAML-ready sequence entry for proteins, nucleic acids, or ligands. Supports FASTA or raw sequence text, optional MSA (A3M or text) for proteins, chemical specifications for ligands, chain duplication, cyclic polymers, and residue-level modifications.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define one molecular entity (protein, DNA, RNA, or ligand) that will go into a Boltz YAML configuration. Typically, you create one or more sequences with this node, optionally add constraints/templates/properties, combine them with a list combiner, and then feed them to the Boltz YAML combiner and prediction nodes.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of molecular entity to define.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity. If multiple_chains is provided, this becomes the first chain in the list.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw sequence text for protein/DNA/RNA. Can be plain sequence or FASTA-formatted text. If FASTA is used here, the header name is extracted automatically.</td><td style="word-wrap: break-word;">>my_protein ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence in FASTA input type (alternative to sequence). The first entry’s header becomes the sequence name; sequence lines are concatenated.</td><td style="word-wrap: break-word;">{'my_protein': '>my_protein\nACDEFGHIKLMNPQRSTVWY'}</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Protein MSA content as plain text (A3M format). If omitted and no msa_a3m is provided, the MSA is set to 'empty'.</td><td style="word-wrap: break-word;">>seq1 ACD- >seq2 ACDE</td></tr>
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Protein MSA provided via A3M input type (alternative to msa_content). The first key’s content is used.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '>seq1\nACD-\n>seq2\nACDE'}</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand specification via SMILES string (for entity_type=ligand). Provide either this or ligand_ccd, not both.</td><td style="word-wrap: break-word;">CCO</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand specification via PDB CCD code (for entity_type=ligand). Provide either this or ligand_smiles, not both.</td><td style="word-wrap: break-word;">HEM</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications for polymer entities. One per line in the form 'position:ccd_code'.</td><td style="word-wrap: break-word;">5:SEP 12:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether the polymer is cyclic. Applicable to protein/DNA/RNA.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional chain IDs for identical copies of this entity. Comma-separated; the final 'id' will be a list if provided.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz-ready sequence mapping for the specified entity. This should be combined with other sequences using a list combiner before building the final YAML.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A', 'B'], 'sequence': 'ACDEFGHIKLMNPQRSTVWY', '_sequence_name': 'my_protein', 'msa': 'empty', 'modifications': [{'position': 5, 'ccd': 'SEP'}], 'cyclic': False}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Protein/DNA/RNA sequences**: You must provide a sequence via 'sequence' or 'sequence_fasta'. If 'sequence' starts with '>' it is treated as FASTA and the header name is extracted.
- **Ligands**: Provide exactly one of 'ligand_smiles' or 'ligand_ccd' when entity_type is 'ligand'. Supplying both will raise an error.
- **MSA handling (proteins)**: If neither 'msa_a3m' nor 'msa_content' is provided, the MSA is set to 'empty'.
- **Chain IDs**: If 'multiple_chains' is set, the 'id' field becomes a list of chain IDs; otherwise it is a single string.
- **Modifications format**: Each line must be 'position:ccd_code' with an integer position; invalid lines are ignored with a warning.
- **Cyclic polymers**: Setting 'cyclic' to true will add a 'cyclic' flag to the entity (polymer only).

## Troubleshooting
- **Error: 'Sequence is required for protein/dna/rna'**: Provide a non-empty 'sequence' or 'sequence_fasta'. If using FASTA, ensure it contains a header and sequence lines.
- **Error: 'SMILES or CCD code is required for ligands'**: When entity_type='ligand', supply either 'ligand_smiles' or 'ligand_ccd'.
- **Error: 'Provide either SMILES or CCD code, not both'**: Remove one of the ligand specifications so only one remains.
- **Unexpected sequence name**: If using 'sequence' with a FASTA header, the header’s first token becomes the sequence name. Adjust the header or use 'sequence_fasta' for clarity.
- **Modifications ignored**: Ensure each line follows 'position:ccd' and that 'position' is an integer (e.g., '12:MSE').
- **Multiple chains not applied**: Provide comma-separated chain IDs with no spaces or ensure you trim spaces (e.g., 'B,C' not 'B, C').
