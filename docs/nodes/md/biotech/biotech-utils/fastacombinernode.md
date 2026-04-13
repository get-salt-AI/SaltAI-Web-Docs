# FASTA Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five FASTA inputs into a single FASTA output. It trims surrounding whitespace for each input, preserves the provided order, and inserts a single newline between non-empty blocks. If no inputs are provided or all are empty, it raises an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/fastacombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to merge multiple FASTA strings produced upstream (for example, separate sequences, chains, or fragments) into one consolidated FASTA string for downstream analysis or prediction steps. It is typically placed after several sequence-generation or editing nodes when you need a single combined FASTA payload.

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
<tr><td style="word-wrap: break-word;">fasta_1</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">First optional FASTA content block to include in the combined output. Leading and trailing whitespace are removed before combining.</td><td style="word-wrap: break-word;">>seqA MKTIIALSYIFCLVFA >seqB GGGGTTTAAACCC</td></tr>
<tr><td style="word-wrap: break-word;">fasta_2</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Second optional FASTA content block. If non-empty, it is appended after fasta_1, separated by a single newline.</td><td style="word-wrap: break-word;">>seqC ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">fasta_3</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Third optional FASTA content block. Included in order if provided and non-empty.</td><td style="word-wrap: break-word;">>seqD MSSSSSSSSSSS</td></tr>
<tr><td style="word-wrap: break-word;">fasta_4</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fourth optional FASTA content block. Included in order if provided and non-empty.</td><td style="word-wrap: break-word;">>seqE TTTTTTTTTTTT</td></tr>
<tr><td style="word-wrap: break-word;">fasta_5</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fifth optional FASTA content block. Included in order if provided and non-empty.</td><td style="word-wrap: break-word;">>seqF VVVVVVVVVVVV</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The concatenated FASTA string composed of all non-empty provided inputs (fasta_1 through fasta_5), with a single newline inserted between each non-empty block.</td><td style="word-wrap: break-word;">>seqA MKTIIALSYIFCLVFA >seqB GGGGTTTAAACCC  >seqC ACDEFGHIKLMNPQRSTVWY</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input required**: The node raises an error if all inputs (fasta_1–fasta_5) are empty or missing; provide at least one non-empty FASTA block.
- **Order preserved**: Inputs are always processed in numeric order (fasta_1 through fasta_5), and the combined output follows this same order.
- **Whitespace handling**: Each provided FASTA block is trimmed of surrounding whitespace; exactly one newline is inserted between consecutive non-empty blocks.
- **No FASTA validation**: The node does not validate headers or sequence characters and assumes each input is already a well-formed FASTA string.
- **No deduplication or record merging**: Headers and sequences are kept as provided; duplicate IDs or overlapping records are not detected or modified.

## Troubleshooting
- **Error: 'At least one FASTA input is required'**: Make sure at least one of fasta_1–fasta_5 contains non-empty text; blocks with only whitespace are treated as empty.
- **Unexpected concatenation layout**: Check that each input FASTA string has the intended internal line breaks and headers starting with '>'; the node only inserts newlines between blocks and does not reformat contents.
- **Missing or extra blank lines**: Remember that the node inserts a single newline between non-empty inputs but does not change line wrapping inside each block. If stricter formatting is required, adjust the upstream nodes or manually fix the FASTA strings before combining.
- **Downstream parser failures**: If a downstream tool cannot parse the combined FASTA, test each individual input FASTA separately to locate malformed headers, invalid characters, or missing line breaks that were carried through unchanged.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/fastacombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

