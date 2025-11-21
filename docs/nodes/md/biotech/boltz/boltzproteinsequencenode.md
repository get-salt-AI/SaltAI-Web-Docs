# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a single protein entry for a Boltz YAML configuration. It parses a FASTA-formatted protein sequence, extracts an optional sequence name from the header, assigns one or more chain IDs, and attaches an MSA reference or uses an 'empty' MSA if none is supplied. Optional post-translational modifications and cyclicity can be specified.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define a protein component before assembling a full Boltz YAML. Connect its output to a list combiner or directly to a YAML combiner along with other components (ligands, constraints, templates, properties). Provide the protein sequence in FASTA format, optionally include an MSA (A3M) and any modifications, then combine and submit for prediction.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein. Must be unique across all chains in the final YAML.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. The header (>name) is used as the sequence name if present; otherwise a default name is assigned.</td><td style="word-wrap: break-word;">>my_protein ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple sequence alignment in A3M format. If omitted or empty, an 'empty' MSA is recorded.</td><td style="word-wrap: break-word;">{'my_alignment.a3m': '>seq1\nACD--EFG\n>seq2\nACDEQEFG'}</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Post-translational or residue modifications, one per line, using 'position:ccd_code'. Positions are 1-based integers.</td><td style="word-wrap: break-word;">10:SEP 25:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Marks the protein as cyclic when true.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional chain IDs if creating identical copies of this protein. Comma-separated list.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one protein entry configured for Boltz YAML. This list item can be combined with other lists and passed into the Boltz YAML combiner.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A', 'B'], 'sequence': 'ACDEFGHIKLMNPQRSTVWY', '_sequence_name': 'my_protein', 'msa': 'empty', 'modifications': [{'position': 10, 'ccd': 'SEP'}], 'cyclic': False}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Chain IDs must be unique across the final YAML. If you add multiple chains here (A,B,C), ensure no other node reuses those IDs.
- FASTA header parsing: if the sequence starts with '>name', that name is used; otherwise a default name is assigned.
- MSA handling: if an A3M is provided it is attached; if omitted or blank, 'empty' is recorded to signal no MSA.
- Modifications must use 'position:ccd_code' per line (e.g., '10:SEP'). Invalid lines are ignored.
- Multiple chains create identical copies of this protein associated with the listed chain IDs.

## Troubleshooting
- Protein sequence is required: Ensure the 'sequence' input contains FASTA text. Empty input will cause an error.
- Invalid modification format: Use 'position:ccd_code' with a 1-based integer position, one per line.
- Duplicate chain ID error later: If the YAML combiner reports duplicate IDs, adjust 'chain_id' or 'multiple_chains' here (and in other nodes) to maintain uniqueness.
- MSA not applied: Verify the A3M input is provided as a valid A3M resource. If blank or malformed, the node will record 'empty' MSA.
- Unexpected sequence name: If no FASTA header is present, a default name is used. Add a '>name' header to control the sequence name.
