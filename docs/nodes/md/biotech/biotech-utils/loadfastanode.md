# Load FASTA

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a FASTA sequence provided as plain text and outputs it as a FASTA-typed value for downstream biotech nodes. It does not modify or validate the content beyond passing it through. Use this to introduce single or multi-record FASTA content into your workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadfastanode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use when you have one or more FASTA records as text and need to feed them into biotech processing nodes (e.g., sequence analysis, conversion, combination). Paste or programmatically supply the FASTA-formatted string; the node outputs a FASTA type that can be combined or transformed by subsequent nodes.

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
<tr><td style="word-wrap: break-word;">fasta_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">FASTA content as a text string. Can contain one or multiple records with headers (lines starting with '>') followed by sequence lines.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILDLWIYHTQGYFP >seq2 GSSHHHHHHSSGLVPRGSHMASMTGGQQMGRDLYDDDDK</td></tr>
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
<tr><td style="word-wrap: break-word;">sequence.fasta</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">The provided FASTA content passed through as a FASTA-typed output for downstream nodes.</td><td style="word-wrap: break-word;">>protein_A MVLSEGEWQLVLHVWAKVEADVAGHGQDILIRLFKSHPETLEKFDR</td></tr>
</tbody>
</table>
</div>

## Important Notes
- FASTA content is passed through without structural validation; ensure it is properly formatted (headers starting with '>' and valid sequence lines).
- Supports multiline input and multiple FASTA records in a single input string.
- This node does not assign IDs; downstream nodes that require IDs typically infer them from FASTA headers.
- For merging multiple sources of FASTA, use the FASTA Combiner node after loading individual strings.

## Troubleshooting
- Invalid FASTA format: Verify each record begins with a header line starting with '>' and that sequence lines contain only valid characters.
- Empty output: Ensure fasta_string is not empty or whitespace-only.
- Downstream node rejects input: Confirm the downstream node expects a FASTA type (string) and not a dictionary-like structure; provide properly formatted headers if IDs are needed.
