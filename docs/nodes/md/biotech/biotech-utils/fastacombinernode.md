# FASTA Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five FASTA inputs into a single FASTA output. It trims surrounding whitespace for each provided input, preserves the provided order (fasta_1 to fasta_5), and inserts a single newline between non-empty blocks. If no inputs are provided or all are empty, it raises an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/fastacombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to merge multiple FASTA strings produced upstream (e.g., separate sequences, chains, or fragments) into one consolidated FASTA string for downstream analysis or prediction steps. It is useful when assembling a final set of sequences from multiple sources.

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
<tr><td style="word-wrap: break-word;">fasta_1</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">First optional FASTA content block to include in the combined output.</td><td style="word-wrap: break-word;">>seqA MKTIIALSYIFCLVFA >seqB GGGGTTTAAACCC</td></tr>
<tr><td style="word-wrap: break-word;">fasta_2</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Second optional FASTA content block to include.</td><td style="word-wrap: break-word;">>seqC ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">fasta_3</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Third optional FASTA content block to include.</td><td style="word-wrap: break-word;">>seqD MSSSSSSSSSSS</td></tr>
<tr><td style="word-wrap: break-word;">fasta_4</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fourth optional FASTA content block to include.</td><td style="word-wrap: break-word;">>seqE TTTTTTTTTTTT</td></tr>
<tr><td style="word-wrap: break-word;">fasta_5</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fifth optional FASTA content block to include.</td><td style="word-wrap: break-word;">>seqF VVVVVVVVVVVV</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The concatenated FASTA string composed of all non-empty provided inputs, separated by a single newline.</td><td style="word-wrap: break-word;">>seqA MKTIIALSYIFCLVFA >seqB GGGGTTTAAACCC >seqC ACDEFGHIKLMNPQRSTVWY</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input required**: The node raises an error if all inputs are empty or missing.
- **Order preserved**: Inputs are processed in numeric order (fasta_1 through fasta_5); the combined output follows this order.
- **Whitespace handling**: Each provided FASTA block is trimmed; a single newline is inserted between non-empty blocks as needed.
- **No validation of FASTA format**: The node does not validate headers or sequence characters; it assumes inputs are well-formed FASTA.
- **No deduplication/merging of records**: Headers and sequences are kept as-is; duplicate IDs or overlapping records are not modified.

## Troubleshooting
- **Error: 'At least one FASTA input is required'**: Provide at least one non-empty FASTA input (e.g., fill fasta_1).
- **Unexpected concatenation issues**: Ensure each input is valid FASTA and includes proper headers starting with '>' and appropriate line breaks within each record.
- **Missing or extra newlines**: The node adds a single newline between non-empty inputs, but it does not reformat internal record line lengths; ensure upstream nodes provide correctly formatted FASTA lines if strict formatting is required.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/fastacombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

