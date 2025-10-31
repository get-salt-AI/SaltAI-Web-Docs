# Colabfold Batch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs batch protein structure prediction using ColabFold from precomputed MSA inputs in A3M format. It aggregates ranked folding results per input sequence and returns predicted structures as PDB content. Note: This node is currently disabled and will raise an error if executed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/ColabfoldBatchNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining multiple sequence alignments (MSAs) with an A3M-based search step (e.g., Colabfold Search or another MSA provider). Provide a dictionary mapping sequence IDs to their A3M content. The node is intended to execute ColabFold predictions in batch and return ranked PDB structures keyed by sequence ID and rank.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA results in A3M format for one or more sequences. Expected as a dictionary mapping sequence IDs to their corresponding A3M content.</td><td style="word-wrap: break-word;">{'seq_A': '>seq_A/1-100\nACDEFGHIK...\n>ref1/1-100\nACDEYGHIK...\n', 'seq_B': '>seq_B/1-95\nMKTLLV...\n>ref2/1-95\nMKTLIV...\n'}</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted ranked structures as a dictionary mapping composite IDs (e.g., seqID_ranked_0.pdb) to PDB file content.</td><td style="word-wrap: break-word;">{'seq_A_ranked_0.pdb': 'MODEL 1\nATOM ...\nENDMDL\n', 'seq_A_ranked_1.pdb': 'MODEL 1\nATOM ...\nENDMDL\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is currently disabled and will raise an error indicating the feature is not available.
- Input must be valid A3M content; invalid or empty A3M entries will cause failures when the feature is enabled.
- Output keys include the original sequence ID combined with a rank suffix (e.g., seqID_ranked_0.pdb).
- Designed for batch processing: provide one or more A3M entries in a single run.

## Troubleshooting
- Error: 'Colabfold Batch is currently disabled' — The feature is not available at this time. Contact support to request access or enablement.
- Unexpected output structure when enabled — Verify that each A3M entry corresponds to a single sequence ID and that the A3M format is valid.
- Missing outputs for some sequences when enabled — Ensure all input A3M entries are non-empty and properly formatted.
