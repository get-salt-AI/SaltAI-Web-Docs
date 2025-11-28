# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a protein sequence entry formatted for Boltz YAML workflows. It accepts a FASTA sequence, optional MSA content, modification annotations, and can assign the sequence to one or multiple chain IDs. The node also extracts a sequence name from a FASTA header when present and defaults the MSA to "empty" if not provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when preparing protein inputs for Boltz structure prediction or design pipelines. Provide a FASTA sequence and optionally an MSA (A3M), specify chain labeling (single or multiple identical chains), and annotate modifications or cyclicity. Connect its output into downstream Boltz configuration builders or predictors.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein. If multiple identical chains are needed, add them via multiple_chains.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. A header (e.g., >name) is optional; if present, it is used as the internal sequence name.</td><td style="word-wrap: break-word;">>my_protein MKTFFVAGVGLAAG...</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple sequence alignment content in A3M format. If omitted, the node sets MSA to "empty". Can be provided as raw text or as a single-key dict containing the content.</td><td style="word-wrap: break-word;">>seq1 MKTFFVAGVGLAAG- >seq2 MKTYFV-GVGLAAG-</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Per-residue modifications, one per line, in the format position:ccd_code. Positions are 1-based integers; CCD is a chemical component identifier.</td><td style="word-wrap: break-word;">5:SEP 12:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the protein as cyclic when true.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated additional chain IDs to create identical copies of this protein.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one dictionary with the protein entry formatted for Boltz YAML: {"protein": {"id": <chain_id or [ids]>, "sequence": <seq>, "_sequence_name": <name>, "msa": <content or "empty">, optional "modifications" and "cyclic"}}.</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A","B"], "sequence": "MKTFFV...", "_sequence_name": "my_protein", "msa": "empty", "modifications": [{"position": 5, "ccd": "SEP"}], "cyclic": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **FASTA header handling**: If the sequence starts with a FASTA header (e.g., >name), that name is captured as _sequence_name; otherwise, it defaults to "sequence".
- **MSA defaulting**: If no MSA is provided, msa is set to "empty". If MSA is provided as a dict, the first key's value is used.
- **Multiple chains**: Setting multiple_chains will produce an id list (e.g., ["A","B"]). Without it, id is a single string.
- **Modifications format**: Each line must be position:ccd_code (e.g., 5:SEP). Lines failing integer parsing for position are ignored with a warning.
- **Cyclicity**: Setting cyclic to true adds a cyclic flag to the protein entry.
- **Validation**: An empty or whitespace-only sequence will raise an error.

## Troubleshooting
- **Empty sequence error**: Ensure the FASTA input is not blank and contains valid amino acid characters after any header.
- **Invalid modification line ignored**: Check that each modification line is in the exact format position:ccd_code (e.g., 12:MSE).
- **Unexpected MSA behavior**: If passing MSA as an object/dict, ensure it contains a single entry with the raw A3M text. Otherwise, provide raw A3M text.
- **Multiple chains not applied**: Verify multiple_chains uses comma-separated IDs without spaces (e.g., B,C) and that chain_id is set.
- **Sequence name missing**: Provide a FASTA header line (e.g., >my_protein) if you want a custom name; otherwise it will default to "sequence".
