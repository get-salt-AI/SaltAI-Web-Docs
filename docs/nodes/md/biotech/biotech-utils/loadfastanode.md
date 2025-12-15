# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a FASTA sequence string into the workflow. It accepts user-provided FASTA text and passes it through unchanged for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a biotech workflow when you need to provide one or more protein/DNA/RNA sequences in FASTA format. Connect its output to any node that expects FASTA input.

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw FASTA content to load. Supports multiline text and can contain one or multiple FASTA records.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQLEERLGLIEVQ >seq2 GASMVENLYFQG</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The FASTA content provided as input, unchanged.</td><td style="word-wrap: break-word;">>my_protein ACDEFGHIKLMNPQRSTVWY</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Pass-through behavior**: The node does not validate or modify the FASTA; it returns exactly what you provide.
- **Multiple records**: You can include multiple sequences; ensure each record starts with a header line beginning with '>'.
- **Formatting**: Maintain standard FASTA formatting (header line, newline-separated sequence lines). Extraneous spaces or invalid characters may cause downstream nodes to fail.

## Troubleshooting
- **Downstream errors parsing FASTA**: Ensure each sequence has a header starting with '>' and sequence lines contain only valid characters for your modality (e.g., A–Z for proteins).
- **Unexpected empty output**: Verify the input field is not blank and contains visible characters (no only whitespace).
- **Multiple sequences not recognized**: Confirm records are separated by newlines and each has its own header line.
