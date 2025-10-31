# FASTA Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five FASTA inputs into a single FASTA string. It preserves the order of provided inputs (fasta_1 to fasta_5), trims surrounding whitespace, and ensures proper newline separation between concatenated blocks.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/FastaCombinerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple FASTA sequences (or multi-record FASTA strings) that need to be merged into a single FASTA input for downstream biotech/protein modeling workflows. Connect any subset of the fasta_1..fasta_5 inputs; empty or unconnected inputs are ignored.

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
<tr><td style="word-wrap: break-word;">fasta_1</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">First FASTA sequence string. Can contain one or more records.</td><td style="word-wrap: break-word;">>seqA MKTAYIAKQRQISFVKSHFSRQ </td></tr>
<tr><td style="word-wrap: break-word;">fasta_2</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Second FASTA sequence string. Optional; ignored if not connected or empty.</td><td style="word-wrap: break-word;">>seqB GHHHHHHHHHHHHHHHHH </td></tr>
<tr><td style="word-wrap: break-word;">fasta_3</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Third FASTA sequence string. Optional.</td><td style="word-wrap: break-word;">>seqC AAAAGGGGCCCC </td></tr>
<tr><td style="word-wrap: break-word;">fasta_4</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fourth FASTA sequence string. Optional.</td><td style="word-wrap: break-word;">>seqD MVLSPADKTNVKAAW </td></tr>
<tr><td style="word-wrap: break-word;">fasta_5</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fifth FASTA sequence string. Optional.</td><td style="word-wrap: break-word;">>seqE TTTTTTTTTT </td></tr>
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
<tr><td style="word-wrap: break-word;">combined_fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">A single FASTA string formed by concatenating all provided inputs in numeric order with proper newline separation.</td><td style="word-wrap: break-word;">>seqA MKTAYIAKQRQISFVKSHFSRQ >seqB GHHHHHHHHHHHHHHHHH </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input must be provided**: If all inputs are empty or missing, the node raises an error.
- **Order preserved**: Inputs are concatenated in the order fasta_1 → fasta_5.
- **Whitespace handling**: Leading/trailing whitespace is trimmed; a newline is inserted between inputs when needed.
- **No validation or deduplication**: Headers and sequences are not modified or validated; duplicates remain as-is.
- **Input limit**: Supports up to five FASTA inputs.

## Troubleshooting
- **Error: 'At least one FASTA input is required'**: Connect at least one non-empty FASTA input.
- **Merged records run together**: The node inserts a newline between inputs if needed, but ensure each input itself is valid FASTA with proper newlines.
- **Unexpected order of sequences**: Verify you connected sequences to the intended slots (fasta_1..fasta_5) to control ordering.
- **Stray blank lines**: Inputs are trimmed; if you see unexpected spacing inside records, check the original FASTA formatting.
- **Duplicate headers**: The node does not modify headers; adjust upstream inputs if unique headers are required.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/FastaCombinerNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

