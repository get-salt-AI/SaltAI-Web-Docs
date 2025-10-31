# Colabfold Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs multiple sequence alignment (MSA) search using the ColabFold pipeline for one or more protein sequences provided in FASTA format, returning alignments in A3M format mapped by sequence IDs. Currently, this node is disabled and will raise an error if used.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/ColabfoldSearchNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to obtain A3M alignments via ColabFold for downstream protein structure prediction steps (e.g., Alphafold). Provide one or more sequences in FASTA; each record will be searched and returned as an A3M entry keyed by the FASTA header. Note: As of now, this node is disabled. Use the MSA Search node instead to perform MSA retrieval.

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
<tr><td style="word-wrap: break-word;">fasta</td><td>True</td><td style="word-wrap: break-word;">FASTA</td><td style="word-wrap: break-word;">Protein sequence(s) in FASTA format to run ColabFold MSA search on. Supports multiple records; each header will become the key in the output.</td><td style="word-wrap: break-word;">>seq1 MKTAYIAKQRQISFVKSHFSRQDILD >seq2 GHHHHHHENLYFQGAMASMTGGQQMGRGS</td></tr>
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
<tr><td style="word-wrap: break-word;">msa.a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A mapping of sequence IDs to their ColabFold MSA results in A3M format.</td><td style="word-wrap: break-word;">{"seq1": ">seq1\nMKTAYIAKQRQISFVKSHFSRQDILD\n>hit1\nMKTA-IAKQRQISFVKSHFSRqdILD", "seq2": ">seq2\nGHHHHHHENLYFQGAMASMTGGQQMGRGS\n>hitA\nGHHHHHHENLYFQGAMAS-TGGQQMGRGS"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Disabled**: This node is currently disabled and will raise a 'NotImplementedError' if executed.
- **Input format**: Provide valid FASTA with unique headers; multiple sequences are supported and processed individually.
- **Output mapping**: Output keys mirror the input FASTA headers; each value is an A3M string.
- **Performance**: When enabled, MSA searches can be time-consuming; long or many sequences may take a long time per entry.
- **Alternative**: Use the 'MSA Search' node as a functional alternative for obtaining A3M alignments.

## Troubleshooting
- **NotImplementedError raised**: The node is disabled. Switch to the 'MSA Search' node or contact support to request ColabFold search enablement.
- **Empty or invalid output**: Ensure the input FASTA is valid (proper headers, only amino acid characters) and that headers are unique.
- **Large runtimes or timeouts**: Reduce sequence length or batch size; consider using a reduced or toy dataset via alternative nodes if available.
- **Mismatched IDs downstream**: Keep FASTA headers consistent across workflow steps; output A3M keys match the input FASTA headers.
