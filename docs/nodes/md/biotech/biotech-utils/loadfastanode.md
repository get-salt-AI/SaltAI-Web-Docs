# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a FASTA-formatted sequence string into the workflow. It is a simple pass-through loader that accepts any provided text and outputs it as a FASTA-type value without validation or transformation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to manually input or paste a protein (or nucleotide) FASTA sequence to start or feed a biotech workflow. Typical use: paste one or more FASTA records here, then connect the output to nodes that consume FASTA (e.g., sequence processing, structure design/fixing, or combiner nodes).

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">FASTA sequence string to upload. Can contain one or more records. The content is not validated or reformatted by this node.</td><td style="word-wrap: break-word;">>seq1 MKTFFVLVLLLALATASA >seq2 GASVVVSDIVKDLGAT</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The provided FASTA string, passed through unchanged.</td><td style="word-wrap: break-word;">>my_protein MSEQNNTEMTFQIQRIYTKDISFEAPNAPHVFQKDW </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Pass-through behavior**: The node returns the input exactly as provided; it does not validate FASTA syntax, parse headers, or standardize line lengths.
- **Multiple records allowed**: You can include multiple FASTA entries. Downstream nodes must support multi-record FASTA if you provide more than one sequence.
- **No metadata attached**: Unlike some other loaders, the output is a plain FASTA string without additional metadata or IDs added by this node.
- **Formatting expectations**: For best compatibility, include standard FASTA headers (lines beginning with '>') and use amino-acid or nucleotide letters as appropriate for downstream tools.

## Troubleshooting
- **Downstream node errors**: If a connected node fails to parse the input, ensure the FASTA is valid (each record starts with a header line beginning with '>' followed by sequence lines).
- **Unexpected output content**: This node does not modify input; verify there are no hidden characters or unsupported symbols in the pasted text.
- **Multiple sequence handling**: If a downstream node expects a single sequence but you provided multiple records, split your input or use a combiner/splitter node appropriately.
- **Line wrapping issues**: Some tools expect wrapped lines (e.g., 60–80 chars). If required, manually wrap the sequence before loading.
