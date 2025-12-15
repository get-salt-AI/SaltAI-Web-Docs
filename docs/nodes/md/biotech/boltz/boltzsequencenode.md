# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs a single molecular sequence entry for Boltz YAML configurations. Supports protein, DNA, RNA, and ligand entities, with optional MSA, cyclic flags, chain duplication, and residue modifications. FASTA input is parsed to extract both the sequence and an optional sequence name.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define one chain or a set of identical chains for a molecular entity before assembling a full Boltz YAML. For proteins/DNA/RNA, provide a sequence (plain or FASTA); for ligands, specify either a SMILES string or a CCD code. Connect the output to a list combiner and then to the Boltz YAML combiner node to build the complete configuration.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity. If multiple copies are needed, use multiple_chains to add more IDs.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Amino acid or nucleotide sequence for protein/DNA/RNA. Supports plain text or FASTA format (header lines starting with '>'). Ignored for ligands.</td><td style="word-wrap: break-word;">>my_protein\nMKTLLILAVVAAASA...</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence provided as a FASTA file-like input (alternative to 'sequence'). The header will be used to set an internal sequence name.</td><td style="word-wrap: break-word;">{'my_seq.fasta': '>protA\\nMKTLLILAVVAAASA...'}</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSA content in string form for proteins. If not provided and no A3M is given, an 'empty' MSA is set. Ignored for non-proteins.</td><td style="word-wrap: break-word;">>seq1\nMKTLLI-AVV\n>seq2\nMKTL-LVAVV</td></tr>
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA in A3M format for proteins (alternative to 'msa_content'). If provided, it takes precedence over 'msa_content'.</td><td style="word-wrap: break-word;">{'alignment.a3m': '>seq1\\nMKTLLI-AVV\\n>seq2\\nMKTL-LVAVV'}</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for ligand entities. Required if entity_type is ligand and 'ligand_ccd' is not provided.</td><td style="word-wrap: break-word;">CC(=O)Oc1ccccc1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">CCD code for ligand entities. Required if entity_type is ligand and 'ligand_smiles' is not provided.</td><td style="word-wrap: break-word;">HEM</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications in the format 'position:ccd_code', one per line. Applies to polymers (protein/DNA/RNA).</td><td style="word-wrap: break-word;">5:PTR\n12:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the polymer as cyclic (applies to protein/DNA/RNA).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated additional chain IDs to represent identical copies of this entity.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one sequence mapping for Boltz YAML. The mapping includes entity type keys ('protein', 'dna', 'rna', or 'ligand') with fields such as 'id', 'sequence' or 'smiles/ccd', optional 'msa', 'modifications', and 'cyclic'.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A', 'B'], 'sequence': 'MKTLLILAVVAAASA...', '_sequence_name': 'protA', 'msa': 'empty', 'modifications': [{'position': 5, 'ccd': 'PTR'}], 'cyclic': False}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequence requirement**: For protein/DNA/RNA, a sequence must be provided via 'sequence' or 'sequence_fasta'; otherwise the node raises an error.
- **Ligand specification**: For ligands, provide exactly one of 'ligand_smiles' or 'ligand_ccd'—using both causes an error.
- **MSA handling**: For proteins, if neither 'msa_a3m' nor 'msa_content' is given, the node sets 'msa' to 'empty'.
- **FASTA parsing**: If input begins with a FASTA header ('>'), the header’s first token becomes the internal sequence name and the sequence lines are concatenated.
- **Multiple chains**: 'multiple_chains' duplicates the same entity across additional chain IDs; the 'id' field becomes a list of chain IDs.
- **Modifications format**: Each line must be 'position:ccd_code' with a valid integer position; invalid lines are ignored with a warning.

## Troubleshooting
- **Error: Sequence is required for protein/dna/rna**: Provide a non-empty 'sequence' or 'sequence_fasta'.
- **Error: Provide either SMILES or CCD code, not both**: For ligand entities, remove one of 'ligand_smiles' or 'ligand_ccd' so only one is set.
- **Unexpected empty sequence after FASTA input**: Ensure the FASTA includes sequence lines below the header and that line breaks are preserved.
- **Modifications not applied**: Check each line is in 'position:ccd_code' format and that positions are integers.
- **MSA not recognized**: When supplying A3M as a file-like input, ensure it is provided as a keyed object (e.g., {'alignment.a3m': '<content>'}); otherwise use 'msa_content' as plain text.
