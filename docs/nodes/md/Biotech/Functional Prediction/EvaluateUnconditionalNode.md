# Evaluate Unconditional

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a set of generated protein structures against one or more folded structures and selects the best folding per design. It also produces a scoring table with key structural metrics (pst, rmsd, pae, plddt) for review. Supports MOCK, PROD, and TEST modes to balance speed, realism, and cost.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Functional Prediction/EvaluateUnconditionalNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating candidate protein structures and obtaining corresponding foldings. Provide both the generated structures and the foldings; the node will compare them, pick the best folding for each design, and output a CSV with evaluation scores. Ideal in workflows where you need an automated selection of best foldings and a quantitative summary of structure quality.

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
<tr><td style="word-wrap: break-word;">generation_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Generated protein structure(s) to evaluate. Accepts one or multiple entries, typically a mapping from design name to PDB content.</td><td style="word-wrap: break-word;">{"design_A": "PDB_CONTENT_STRING_OR_PATH", "design_B": "PDB_CONTENT_STRING_OR_PATH"}</td></tr>
<tr><td style="word-wrap: break-word;">folding_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted folding structure(s) to evaluate the generated proteins against. Provide one or more PDBs, typically a mapping from folding name to PDB content.</td><td style="word-wrap: break-word;">{"design_A_ranked_0": "PDB_CONTENT_STRING_OR_PATH", "design_B_ranked_0": "PDB_CONTENT_STRING_OR_PATH"}</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Execution mode. Options: MOCK (returns predefined sample results for quick demos), PROD (runs the full backend evaluation), TEST (runs a minimal version, limiting evaluation to the first structure in each input for faster turnaround).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">best_folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">The best folding selected per input design. Returned as a mapping from design name to PDB content.</td><td style="word-wrap: break-word;">{"design_A": "PDB_CONTENT_STRING", "design_B": "PDB_CONTENT_STRING"}</td></tr>
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">A CSV string summarizing evaluation metrics per structure (includes columns such as pst, rmsd, pae, plddt).</td><td style="word-wrap: break-word;">name,pst,rmsd,pae,plddt\ndesign_A,0.85,1.9,2.3,92.1\ndesign_B,0.79,2.4,3.1,89.5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Mode behavior: MOCK returns canned data; PROD runs the full evaluation; TEST limits processing to the first structure in each input to reduce runtime.
- Provide at least one PDB in both generation_pdb and folding_pdb. If many structures are provided, processing time scales accordingly.
- Output best_folding.pdb contains one entry per evaluated design, mapping the design name to its selected best folding.
- The score.csv output is a CSV string; save it to a file or parse it downstream if you need structured access to metrics.
- Ensure inputs are valid PDB contents or resolvable PDB payloads as accepted by your environment.

## Troubleshooting
- No results or empty outputs: Verify both generation_pdb and folding_pdb contain at least one valid PDB entry each.
- Unexpectedly long runtime: Use TEST mode to validate the setup or reduce the number of input structures.
- Results look synthetic: Ensure mode is set to PROD instead of MOCK.
- Missing best folding for a design: Confirm that the folding_pdb input includes the relevant foldings for the corresponding generated design names.
- CSV parsing issues: Remember score.csv is returned as a string; write it to a .csv file or parse it with a CSV reader.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Functional Prediction/EvaluateUnconditionalNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

