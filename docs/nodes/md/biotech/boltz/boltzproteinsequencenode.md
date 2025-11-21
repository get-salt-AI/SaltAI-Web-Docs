# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a protein entry for Boltz YAML workflows. It accepts a FASTA protein sequence and optional MSA, supports multiple identical chain copies, and allows specifying residue-level modifications and cyclic peptides. The node outputs a structured protein specification ready to be combined with other Boltz components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define a protein entity for downstream Boltz prediction or diffusion nodes. Provide a chain ID and a protein sequence (FASTA). Optionally add an A3M MSA, list additional chain IDs if the same sequence should be replicated across chains (e.g., B,C), declare post-translational modifications, and mark the protein as cyclic if applicable. Chain together with ligand, constraint, template, and property builders, then feed into a Boltz combine/predict node.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein. If the same sequence should appear on multiple chains, specify them via 'multiple_chains'.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. A header line starting with '>' is optional; if present, it is used as the internal sequence name. The sequence must not be empty.</td><td style="word-wrap: break-word;">>my_protein MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQ</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple sequence alignment in A3M format. Leave empty to use an 'empty' MSA placeholder. Can be provided directly as A3M text or as a mapping from ID to A3M content produced by an upstream MSA node.</td><td style="word-wrap: break-word;">>seq1/1-100 MKTAYIAKQRQISFVKSHF--RQLEERLGLIEVQ >seq2/1-100 MK-AYIAKQRQISFVKSHF--RQLEERLGLIEVQ</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue modifications, one per line, in the format 'position:ccd_code'. Positions are 1-based. Lines with invalid formats are ignored.</td><td style="word-wrap: break-word;">5:SEP 42:MSE</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Mark the protein as cyclic.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs that should be identical copies of the provided sequence.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single structured protein specification for Boltz YAML. The structure includes keys like 'protein.id' (string or list of chain IDs), 'protein.sequence', optional 'protein.msa', optional 'protein.modifications', and optional 'protein.cyclic'.</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A","B","C"], "sequence": "MKTAYIAK...", "_sequence_name": "my_protein", "msa": "empty", "modifications": [{"position": 5, "ccd": "SEP"}], "cyclic": false}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **FASTA handling**: If a header (starting with '>') is present, it is used as the internal sequence name; otherwise a default name is applied.
- **MSA default**: When 'msa' is not provided or empty, the node sets the MSA to 'empty'.
- **MSA input forms**: The node accepts either raw A3M text or a mapping {id: a3m_content}; in the latter case, the first entry is used.
- **Multiple chains**: Use 'multiple_chains' (e.g., B,C) to replicate the same sequence across chains; the output protein.id becomes a list.
- **Modifications format**: Each line must be 'position:ccd_code' with a 1-based integer position; invalid lines are ignored.
- **Cyclic proteins**: Set 'cyclic' to true to indicate a cyclic peptide.
- **Required sequence**: The protein sequence must be non-empty after parsing; otherwise the node raises an error.

## Troubleshooting
- **Error: Protein sequence is required**: Ensure 'sequence' is provided and not just a header; include at least one amino-acid character line.
- **Invalid modification format**: Verify each line follows 'position:ccd_code' and positions are integers (e.g., '5:SEP').
- **Unexpected MSA behavior**: If passing a dict from an upstream node, confirm it contains at least one entry. If empty or not provided, the node sets MSA to 'empty'.
- **Chain ID issues**: Provide a single base 'chain_id' (e.g., A) and add replicas in 'multiple_chains' as a comma-separated list without spaces or with spaces trimmed (e.g., B,C).
- **Sequence name mismatch**: If you rely on sequence names across components, ensure the FASTA header aligns with expectations; otherwise a default name is used.
