# Boltz Protein Sequence

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts a protein sequence (provided in FASTA format) and optional MSA plus annotations into a structured dictionary suitable for Boltz YAML. It parses the FASTA header to derive a sequence name, normalizes the raw sequence, attaches MSA content and its inferred format, and encodes chain identifiers, cyclicity, and residue-level modifications. The output is a single-element list containing a protein sequence specification, ready to be combined with other Boltz components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzproteinsequencenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to define one or more protein chains for a Boltz-based protein design or structure prediction workflow. Typical usage is: (1) load or paste a protein FASTA with a header (for example, from UniProt or a de novo design), (2) optionally connect an MSA file constructed upstream, and (3) specify chain IDs and any chemical modifications. The output is usually sent into a BoltzListCombinerNode (possibly alongside BoltzLigandSequenceNode and BoltzConstraintNode outputs), then into BoltzYAMLCombinerNode to build a full Boltz YAML configuration, which finally feeds into BoltzPredictNode.

Common upstream nodes include FASTA loaders or sequence generators that output FASTA-typed data, and nodes that generate MSA content (A3M/CSV) as MSA-typed outputs. Downstream, connect the resulting protein_sequence output into one of the inputs of BoltzListCombinerNode; that combined list then flows to BoltzYAMLCombinerNode. In multimer workflows, you may use multiple BoltzProteinSequenceNode instances with distinct chain_id and multiple_chains settings to define complex stoichiometries. Best practices: ensure chain IDs are globally unique across all sequences in the final YAML, keep modifications well-formatted (one "position:ccd_code" per line), and ensure the FASTA actually contains the intended amino acid sequence without trailing whitespace.

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
<tr><td style="word-wrap: break-word;">chain_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary chain identifier for this protein entity. This is the Boltz chain ID, typically a single uppercase letter but can be any non-empty string. Must be globally unique across all protein, nucleic acid, and ligand IDs in the final Boltz YAML unless you intentionally define identical copies via multiple_chains.</td><td style="word-wrap: break-word;">A</td></tr>
<tr><td style="word-wrap: break-word;">sequence</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence in FASTA format. Can include a header line starting with '>' followed by an identifier; the node extracts this identifier (first whitespace-delimited token, with commas removed) as _sequence_name and concatenates all non-header lines as the amino acid sequence. If the content does not start with '>', it is treated as raw sequence, but the node still strips whitespace. Empty or whitespace-only values will raise an error.</td><td style="word-wrap: break-word;">>sp\|P69905\|HBA_HUMAN Hemoglobin subunit alpha VLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDL</td></tr>
<tr><td style="word-wrap: break-word;">msa</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Multiple sequence alignment for this protein, in A3M or CSV-like format depending on upstream generation. The node infers the MSA content and format using an internal helper. If provided and non-empty, the raw alignment text will later be moved to an auxiliary file and replaced with a file path in Boltz YAML. If omitted or representing an 'empty' MSA, Boltz will run with minimal or no alignment information.</td><td style="word-wrap: break-word;">>query VLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDL >homolog1 VLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDL</td></tr>
<tr><td style="word-wrap: break-word;">modifications</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue-level chemical modifications, one per line, in the format 'position:ccd_code'. 'position' is a 1-based integer residue index in the protein sequence; 'ccd_code' is a three-letter component code recognized by Boltz. Lines without a colon or with a non-integer position are ignored with a warning. If at least one valid line is parsed, a 'modifications' array is added to the protein entry.</td><td style="word-wrap: break-word;">5:MSE 42:SEP 100:TPO</td></tr>
<tr><td style="word-wrap: break-word;">cyclic</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether the protein should be treated as cyclic (N- to C-terminus connected). When true, the output protein object includes 'cyclic': true, which Boltz uses to adjust how the chain is modeled.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">multiple_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of additional chain IDs for identical copies of this protein sequence (for example, to define a homodimer or homotetramer). The primary chain_id plus these extra IDs form either a single string (for one chain) or a list of strings (for multiple) in the output 'id' field. Each entry is trimmed; empty entries are ignored. These chain IDs must not collide with IDs from other sequence nodes in the same workflow.</td><td style="word-wrap: break-word;">B,C</td></tr>
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
<tr><td style="word-wrap: break-word;">protein_sequence</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single-element list containing one protein sequence specification dictionary for Boltz YAML. The typical structure is: [{"protein": {"id": <chain_id or [chain_ids]>, "sequence": <amino_acid_string>, "_sequence_name": <name_from_fasta>, "msa": <msa_content_or_filename>, "_msa_format": <format_string>, "modifications": [{"position": int, "ccd": str}], "cyclic": true}}]. Downstream nodes such as BoltzListCombinerNode and BoltzYAMLCombinerNode treat this as a generic Boltz sequence object when building the final YAML and auxiliary files.</td><td style="word-wrap: break-word;">[{"protein": {"id": ["A", "B"], "sequence": "VLSPADKTNVKAAWGKVGAHAGEYGAEALERMFLSFPTTKTYFPHFDL", "_sequence_name": "HBA_HUMAN", "msa": "msa_1.a3m", "_msa_format": "a3m", "modifications": [{"position": 5, "ccd": "MSE"}], "cyclic": false}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large MSAs (many sequences or long alignments) do not significantly impact this node but will increase memory and runtime for downstream prediction in BoltzPredictNode.
- **Limitations**: The node expects a FASTA-typed sequence input; if you pass in content that does not start with '>' you will not get a descriptive _sequence_name (it defaults to 'sequence').
- **Behavior**: If the FASTA header contains commas, they are stripped from the derived _sequence_name, and only the first token before whitespace is used, which can change the identifier you see downstream.
- **Behavior**: Incorrectly formatted modification lines (for example, 'A5:MSE' or 'five:MSE') are skipped with a warning rather than causing an error, so intended modifications may be silently ignored if not well formatted.
- **Limitations**: Global chain ID uniqueness is not enforced here; conflicts between chain_id or multiple_chains values from different nodes are only detected later by BoltzYAMLCombinerNode.

## Troubleshooting
- **Common Error 1**: "Protein sequence is required" – This arises when the sequence input is empty or only whitespace. Ensure the upstream FASTA source is connected and contains a non-empty sequence.
- **Common Error 2**: "Protein sequence cannot be empty" – Occurs when the provided FASTA has a header but no sequence lines beneath it. Add the amino acid sequence lines following the '>' header.
- **Common Error 3**: Unexpected or generic _sequence_name – If your FASTA header is long, only the first token (up to the first space) is used and commas are removed. Edit the header to a clean identifier (for example, '>MyDesign01') if you rely on it downstream.
- **Common Error 4**: Duplicate chain ID errors later – If you see a duplicate chain ID error from BoltzYAMLCombinerNode, revisit all BoltzProteinSequenceNode and BoltzLigandSequenceNode instances to choose non-overlapping chain_id and multiple_chains values.
