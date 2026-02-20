# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a protein sequence object formatted for Boltz YAML workflows. It parses a provided protein FASTA, extracts the sequence and optional header as a sequence name, and attaches optional MSA, modifications, cyclic flag, and multiple chain IDs. Output is a ready-to-combine sequence payload for downstream Boltz YAML assembly and prediction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to define a protein sequence for Boltz-based structure prediction or affinity workflows. Typically, you will: 1) provide a protein FASTA (with or without a header), 2) optionally attach an MSA and modifications, 3) configure chain IDs (and duplicates if needed), and 4) feed the resulting output into Boltz List Combiner -> Boltz YAML Combiner -> Boltz Predict.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein (single letter or string).</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. If a header is present (e.g., '>my_protein'), it will be used as the internal sequence name; only the sequence lines are kept.</td><td style="word-wrap: break-word;">>my_protein_A MKTLLILAVAAALAAGASA... (AA sequence lines)</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Optional multiple sequence alignment content in A3M or CSV format. Leave empty to indicate an 'empty' MSA.</td><td style="word-wrap: break-word;">>seq1 MKTLLI---AVAAALA >seq2 MKTLFIVAAAVAAALA</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional residue-level modifications, one per line, using 'position:ccd_code' format. Positions are 1-based indices.</td><td style="word-wrap: break-word;">5:CSO 12:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Set true if the protein is cyclic.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs to create identical copies of this protein (e.g., for homomers).</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A protein sequence object (as a list with one item) containing fields like id (single or list of chain IDs), sequence, optional _sequence_name, optional msa and _msa_format, optional modifications, and cyclic.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A', 'B'], 'sequence': 'MKTLLILAVAAALAAGASA...', '_sequence_name': 'my_protein_A', 'msa': 'empty', '_msa_format': 'a3m', 'modifications': [{'position': 5, 'ccd': 'CSO'}], 'cyclic': False}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Sequence header handling: If the FASTA header is present, it will be used as the internal sequence name; otherwise a default name is assigned.
- MSA handling: The node determines and stores the MSA format (e.g., 'a3m' or 'csv'); leave MSA empty to indicate an 'empty' MSA.
- Multiple chains: Provide comma-separated chain IDs to create identical copies of this protein (e.g., homomers).
- Modifications format: Use 'position:ccd_code' per line (e.g., '5:CSO'); invalid lines are ignored with warnings.
- Cyclic flag: Set 'cyclic' to true if the protein is a cyclic polymer.
- Validation: The protein sequence is required and cannot be empty; FASTA content is parsed to extract only sequence lines.
- Combining later: When assembling the final YAML, ensure all chain IDs across all sequences are unique to avoid validation errors in downstream nodes.

## Troubleshooting
- Protein sequence is required: Ensure 'sequence' contains a valid FASTA. If you only have plain sequence text, include it as FASTA (with or without a header).
- Empty or whitespace sequence: Remove extra whitespace and verify the FASTA has sequence lines after any header.
- Invalid modifications: Confirm each line follows 'position:ccd_code' and positions are integers (1-based).
- Unexpected MSA errors: Provide A3M or CSV content; if unavailable, leave MSA empty to use 'empty' MSA.
- Duplicate chain IDs in later steps: If YAML combination fails due to duplicate IDs, adjust 'chain_id' or 'multiple_chains' here so all chains are unique in the final workflow.
