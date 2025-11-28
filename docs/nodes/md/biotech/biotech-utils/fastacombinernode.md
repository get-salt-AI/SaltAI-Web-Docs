# FASTA Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five FASTA inputs into a single FASTA string. It preserves the original headers and sequences, trims surrounding whitespace, and inserts a single newline between non-empty inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/fastacombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple FASTA sequences (or multi-record FASTA blocks) that need to be merged into one input for downstream protein or bioinformatics processing. Typically placed after sequence loaders or extractors and before nodes that accept a unified FASTA.

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
<tr><td style="word-wrap: break-word;">fasta_1</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">First FASTA sequence or multi-record FASTA block to include in the combined output.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQ >seq2 GGLSDGEWQQVLNVWGKVEAD</td></tr>
<tr><td style="word-wrap: break-word;">fasta_2</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Second FASTA sequence(s) to append.</td><td style="word-wrap: break-word;">>seq3 ACDEFGHIKLMNPQRSTVWY</td></tr>
<tr><td style="word-wrap: break-word;">fasta_3</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Third FASTA sequence(s) to append.</td><td style="word-wrap: break-word;">>seq4 GGGGGGGGGG</td></tr>
<tr><td style="word-wrap: break-word;">fasta_4</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fourth FASTA sequence(s) to append.</td><td style="word-wrap: break-word;">>seq5 MTDKEK</td></tr>
<tr><td style="word-wrap: break-word;">fasta_5</td><td>False</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Fifth FASTA sequence(s) to append.</td><td style="word-wrap: break-word;">>seq6 VVVVVVVVVV</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">A single FASTA string containing all provided inputs, separated by newlines.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQ >seq2 GGLSDGEWQQVLNVWGKVEAD >seq3 ACDEFGHIKLMNPQRSTVWY</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input**: If all inputs are empty or unset, the node raises an error: "At least one FASTA input is required".
- **Whitespace handling**: Each provided FASTA is trimmed; the node inserts a single newline between blocks if needed.
- **No validation/formatting**: The node does not validate FASTA correctness or re-wrap lines; it simply concatenates provided content.
- **Order matters**: Inputs are appended in order from fasta_1 to fasta_5.
- **Input limit**: The node exposes five optional FASTA inputs; use additional combiners or upstream batching for more.

## Troubleshooting
- **Error: At least one FASTA input is required**: Connect at least one non-empty FASTA to any of the inputs.
- **Malformed FASTA output downstream**: Ensure each input uses proper FASTA headers (lines starting with '>') and sequences; this node does not fix formatting.
- **Unexpected missing newline between records**: Confirm that each input either ends with a newline or allow the node to insert one by keeping inputs as plain FASTA blocks; avoid trailing spaces.
- **Need more than five inputs**: Chain multiple FASTA Combiner nodes (e.g., combine groups of five, then combine the results).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/fastacombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

