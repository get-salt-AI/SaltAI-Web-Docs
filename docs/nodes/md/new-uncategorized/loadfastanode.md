# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a FASTA sequence string into the workflow. It accepts multiline text and passes it through unchanged as a FASTA-typed output. No validation or parsing is performed; the node simply outputs exactly what you provide.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a biotech workflow when you already have a FASTA sequence (or multiple records) and need to provide it to downstream nodes for tasks like sequence analysis, structure prediction, or conversion. Paste or programmatically supply the FASTA text, then connect the output to nodes that consume FASTA.

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The FASTA content to upload. Can include one or multiple FASTA records, with standard '>' headers and sequence lines. Content is not validated or reformatted.</td><td style="word-wrap: break-word;">>seq1 MGSSHHHHHHSSGLVPRGSHMASMTGGQQMGRGSEF-EXAMPLE >seq2 GATTACAGATTACAGATTACA</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The provided FASTA content, passed through unchanged as a FASTA-typed value.</td><td style="word-wrap: break-word;">>my_protein MKTAYIAKQRQISFVKSHFSRQDILD-EXAMPLE</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node performs no format validation; ensure your input follows FASTA conventions (header lines start with '>' followed by sequence lines).
- Outputs exactly the input text (including headers, line wrapping, and whitespace). Downstream behavior depends on the correctness of your FASTA.
- Supports multiple FASTA records in a single input string.
- Large inputs are accepted as plain text; consider memory limits if supplying very large multi-record FASTA files.

## Troubleshooting
- If downstream nodes fail, verify headers and sequences are valid FASTA (no illegal characters, proper '>' headers).
- If the output appears empty, ensure the input field is filled and not just whitespace.
- If line wrapping causes issues in other tools, reformat your FASTA before loading (this node will not modify wrapping).
- If sequences map incorrectly downstream, confirm that record IDs in headers match the expectations of those nodes.
