# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a protein sequence entry for Boltz YAML workflows. It accepts a required protein sequence (FASTA format), supports optional MSA (A3M or plain text), residue modifications, a cyclic flag, and duplication across multiple chain IDs. FASTA headers are parsed to name the sequence automatically; MSA defaults to 'empty' when not provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to create a protein sequence specification before combining it with other sequences, constraints, templates, and properties in a Boltz workflow. Typical usage: set the primary chain ID and FASTA sequence, optionally attach an MSA, add residue modifications or mark as cyclic, and specify additional chain IDs when the same chain should be duplicated (e.g., homomers). Feed the resulting sequence list into a Boltz YAML Combiner or downstream prediction nodes.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein (single character or label).</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. If a header is present, it is used to derive the sequence name.</td><td style="word-wrap: break-word;">>my_protein MSEQUENCEAA...</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple sequence alignment in A3M format (or plain text). If omitted or empty, the MSA is set to 'empty'.</td><td style="word-wrap: break-word;">>seq1/1-100 M---SE-QUEN-CE >seq2/1-100 MABCSE-QUEN-CE</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level modifications, one per line, formatted as 'position:ccd_code'. Positions must be integers; invalid lines are ignored.</td><td style="word-wrap: break-word;">5:ABC 12:XYZ</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the protein as cyclic.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional chain IDs (comma-separated) to duplicate this sequence across multiple chains.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one protein sequence specification object suitable for Boltz YAML assembly.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MSEQUENCE...", "_sequence_name": "my_protein", "msa": "empty", "modifications": [{"position": 5, "ccd": "ABC"}], "cyclic": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Sequence is required and cannot be empty. If a FASTA header exists, it is used as the sequence name; otherwise a default name is assigned.
- If MSA is provided as an uploaded A3M file, the file content is used; if omitted or blank, the node sets msa to 'empty'.
- When multiple_chains is provided, the 'id' field becomes a list of chain IDs; otherwise it remains a single string.
- Modifications must be provided as 'position:ccd_code' per line; non-integer positions or malformed lines are ignored.
- This node outputs a list with a single protein entry, designed to be combined with other entries by downstream Boltz YAML assembly nodes.

## Troubleshooting
- Error: 'Protein sequence is required' or 'Protein sequence cannot be empty' — Ensure the FASTA input is provided and not blank.
- Unexpected name or missing name — Confirm the FASTA header line starts with '>' and contains a valid identifier.
- MSA not used — Provide a valid A3M file or non-empty text; otherwise the node sets msa to 'empty'.
- Modifications ignored — Ensure each line follows 'position:ccd_code' with an integer position.
- Duplicated chains not appearing — Verify multiple_chains uses comma-separated IDs without spaces (e.g., 'B,C').
