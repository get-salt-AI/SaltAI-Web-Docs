# Boltz Sequence Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a single molecular sequence entry for use in Boltz YAML workflows. Supports proteins, DNA, RNA, and ligands, including chain IDs, multiple identical chains, optional MSA for proteins, residue modifications, and cyclic polymers. Outputs a list containing one standardized sequence mapping ready to be combined into a full Boltz configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define each molecular entity in your system before assembling the final Boltz YAML. For proteins/DNA/RNA, provide the sequence (as raw string or FASTA). For ligands, provide either a SMILES string or a CCD code. If you have multiple identical chains (e.g., A,B), use the multiple_chains input. For proteins, you can attach MSA content (A3M file or raw string). Feed the resulting 'sequences' output into a list combiner and then into the Boltz YAML combiner.

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
<tr><td style="word-wrap: break-word;">entity_type</td><td>True</td><td style="word-wrap: break-word;">one of: protein \| dna \| rna \| ligand</td><td style="word-wrap: break-word;">Type of molecular entity to define.</td><td style="word-wrap: break-word;">protein</td></tr>
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this entity. If multiple copies are needed, add them via multiple_chains.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence string for protein/DNA/RNA. Can be a raw sequence or FASTA text. If FASTA text is provided here, the header name will be used and the sequence extracted automatically.</td><td style="word-wrap: break-word;">MKTAYIAKQRQISFVKSHFSRQDILD...</td></tr>
<tr><td style="word-wrap: break-word;">sequence_fasta</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Sequence provided as a FASTA file/object (alternative to 'sequence'). The header name is stored, and the sequence is extracted.</td><td style="word-wrap: break-word;">{'seq_proteinA': '>seq_proteinA\nMKTAYI...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">msa_content</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSA content as a raw string (protein only). Leave empty to mark MSA as 'empty'. If 'msa_a3m' is provided, it takes precedence.</td><td style="word-wrap: break-word;">>seq1/100-200 ACDE... >seq2/101-201 AC-E... </td></tr>
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA in A3M format (protein only). If provided, overrides 'msa_content'.</td><td style="word-wrap: break-word;">{'msaA': '>seq1\nACDE...\n>seq2\nAC-E...\n'}</td></tr>
<tr><td style="word-wrap: break-word;">ligand_smiles</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SMILES string for ligand entities. Provide this or 'ligand_ccd', not both.</td><td style="word-wrap: break-word;">CC(=O)OC1=CC=CC=C1C(=O)O</td></tr>
<tr><td style="word-wrap: break-word;">ligand_ccd</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">CCD (Chemical Component Dictionary) code for ligand entities. Provide this or 'ligand_smiles', not both.</td><td style="word-wrap: break-word;">ATP</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications for protein/DNA/RNA. One per line in the form position:ccd_code.</td><td style="word-wrap: break-word;">5:MSE 42:SEP</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the polymer as cyclic (applies to protein/DNA/RNA).</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">sequences</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one standardized sequence mapping for the specified entity. Includes chain id(s) and the appropriate fields (sequence or ligand spec, MSA if protein, modifications, cyclic).</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A', 'B'], 'sequence': 'MKTAYIAKQRQISFVKSHFSRQDILD...', '_sequence_name': 'protein_sequence', 'msa': 'empty', 'modifications': [{'position': 5, 'ccd': 'MSE'}], 'cyclic': False}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequence requirement**: For protein/DNA/RNA, you must provide a sequence via 'sequence' or 'sequence_fasta'.
- **Ligand exclusivity**: For ligands, provide exactly one of 'ligand_smiles' or 'ligand_ccd'—not both.
- **Multiple chains**: Use 'multiple_chains' for identical copies (e.g., A with additional B,C). All listed IDs will be included under a single entity.
- **MSA handling**: If no MSA is provided for proteins, the node sets MSA to the literal value 'empty'.
- **FASTA headers**: When a FASTA is provided, the header name (without commas) is stored as '_sequence_name'.
- **Modifications format**: Each modification line must be 'position:ccd_code' with a valid integer position.
- **Downstream uniqueness**: Final YAML requires unique chain IDs across all sequences; ensure IDs don’t conflict when combining multiple entities.

## Troubleshooting
- **Error: 'Sequence is required for protein/dna/rna'**: Provide a valid sequence string or FASTA via 'sequence' or 'sequence_fasta'.
- **Error: 'SMILES or CCD code is required for ligands'**: Set 'ligand_smiles' or 'ligand_ccd' (exactly one).
- **Error: 'Provide either SMILES or CCD code, not both'**: Remove one of the ligand fields so only a single specification remains.
- **Invalid modification format warning**: Ensure each line in 'modifications' follows 'position:ccd_code' with a numeric position (e.g., '42:SEP').
- **Unexpected FASTA in 'sequence'**: If pasting FASTA into 'sequence', the node will extract header and sequence automatically. Verify the header is correct and the lines contain only sequence characters.
- **Multiple chains not applied**: Confirm 'multiple_chains' uses comma-separated IDs without spaces (e.g., 'B,C').
- **Downstream duplicate chain ID error**: When later combining into YAML, ensure this node’s chain IDs do not overlap with other entities’ chain IDs.
