# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a protein or nucleotide sequence provided in FASTA format and outputs it as a FASTA-type value. The node does not modify or validate the content; it simply forwards the provided text for use by downstream biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a biotech workflow when you have a FASTA-formatted sequence as text. Paste or provide the complete FASTA string (including header if available) to feed into alignment, structure prediction, or sequence processing nodes.

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The FASTA sequence string to upload. Typically includes an optional header line starting with '>' followed by one or more lines of sequence characters.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILDLWIYHTQGYFPDWQNY </td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The provided FASTA string, passed through unchanged for use by subsequent nodes.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILDLWIYHTQGYFPDWQNY </td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node does not validate FASTA syntax; malformed input may cause downstream nodes to fail.
- Supports multiline string input, allowing standard FASTA headers and multi-line sequences.
- If multiple sequences are needed, use a combiner or batching node downstream; this node emits a single FASTA string.

## Troubleshooting
- If downstream nodes report parsing errors, verify the FASTA format: header (optional) begins with '>', and sequence lines contain only valid characters.
- Remove extra whitespace or hidden characters that may have been introduced by copy-paste.
- Ensure the sequence length and alphabet (amino acids vs nucleotides) match the expectations of downstream nodes.
