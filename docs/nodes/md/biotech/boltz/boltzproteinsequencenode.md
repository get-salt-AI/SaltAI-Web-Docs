# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a protein sequence object formatted for Boltz YAML. It accepts a FASTA-formatted protein sequence, optional MSA (A3M), and optional annotations like modifications, cyclicity, and multiple identical chain IDs. The node parses the FASTA header to set a sequence name and ensures an MSA is present (uses "empty" if none is provided).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to prepare a single protein chain (or identical copies across multiple chains) for Boltz workflows. Feed the resulting output into a list combiner (if aggregating multiple components) and then into a YAML combiner to form a complete Boltz YAML configuration before running prediction or partial diffusion.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unique chain identifier for this protein. If multiple identical copies are needed, provide them via 'multiple_chains'.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. The header (if present) is used to derive the sequence name; the sequence lines are concatenated as the sequence content.</td><td style="word-wrap: break-word;">>my_protein ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple Sequence Alignment in A3M format. Provide as text or as a file-mapped object. If omitted or empty, the node sets MSA to 'empty'.</td><td style="word-wrap: break-word;">>seq1/1-20 ACDEFGHIKLMNPQRSTVWY >seq2/1-20 ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Per-residue modifications, one per line, in the format 'position:ccd_code'. Positions must be integers.</td><td style="word-wrap: break-word;">5:CSO 10:TPO</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the protein as cyclic.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs for identical copies of this protein. The output 'id' becomes a list when multiple chains are declared.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one protein sequence mapping ready for Boltz YAML. Structure: [{"protein": {"id": <string or list>, "sequence": <string>, "_sequence_name": <string>, "msa": <string or 'empty'>, "modifications"?: [...], "cyclic"?: true}}].</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A","B"], "sequence": "ACDEFGHIKLMNPQRSTVWY", "_sequence_name": "my_protein", "msa": "empty", "modifications": [{"position": 5, "ccd": "CSO"}]}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Sequence is required**: The FASTA input must not be empty. The sequence content is extracted from the FASTA body.
- **FASTA header handling**: If the sequence begins with '>', the header's first token becomes '_sequence_name'. Otherwise a default name 'sequence' is used.
- **MSA defaulting**: If no MSA is provided or it is empty, the node sets 'msa' to 'empty' to comply with downstream requirements.
- **MSA input forms**: MSA can be provided as text or as a file-mapped object; the node will extract the first entry if a mapping is provided.
- **Multiple chains**: Supplying 'multiple_chains' creates identical copies of the protein with the specified chain IDs; the 'id' field becomes a list.
- **Modifications format**: Each line must be 'position:ccd_code' with an integer position. Invalid lines are ignored with a warning and not added.
- **Cyclic proteins**: Setting 'cyclic' to true adds a 'cyclic' flag to the output.

## Troubleshooting
- **Error: 'Protein sequence is required'**: Ensure the 'sequence' input is provided and not empty; include FASTA header and body if using FASTA format.
- **Error: 'Protein sequence cannot be empty'**: The FASTA header alone is not enough—include sequence lines beneath the header.
- **MSA not recognized or ignored**: If providing a mapping, confirm it contains at least one key->content pair. If providing text, ensure it is non-empty; otherwise it will default to 'empty'.
- **Unexpected default sequence name**: If you expect a custom name, include a FASTA header line starting with '>' (e.g., '>my_name'). Without a header, the name defaults to 'sequence'.
- **Multiple chains not applied**: Provide 'multiple_chains' as a comma-separated list without spaces or trim spaces (e.g., 'B,C' or 'B, C').
- **Modifications missing**: Verify each line uses 'position:ccd_code' and the position is an integer (e.g., '15:CSO'). Invalid lines are skipped.
