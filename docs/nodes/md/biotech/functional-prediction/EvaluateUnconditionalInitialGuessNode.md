# Evaluate Unconditional Initial Guess

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates generated protein structures against provided foldings while incorporating an initial guess score table to prioritize candidates. It selects the best folding per protein and produces a CSV with key evaluation metrics (e.g., pst, rmsd, pae, plddt). Supports MOCK, PROD, and TEST modes for flexibility and quick iteration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/EvaluateUnconditionalInitialGuessNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have: (1) a set of generated protein structures, (2) corresponding predicted foldings, and (3) the score table from an initial guess prediction step. It compares foldings to designs, leverages the initial guess scores to assess prediction quality, selects the best folding per design, and outputs both the best PDBs and a CSV of evaluation scores. In quick pipelines, use TEST mode to process only the first structure; in production runs, use PROD.

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
<tr><td style="word-wrap: break-word;">generation_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A mapping of design names to PDB text for the generated proteins to evaluate.</td><td style="word-wrap: break-word;">{'design_A': 'ATOM ...\nEND\n', 'design_B': 'ATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">folding_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A mapping of folding names to PDB text for candidate foldings to evaluate against the generated proteins.</td><td style="word-wrap: break-word;">{'design_A_ranked_0': 'ATOM ...\nEND\n', 'design_A_ranked_1': 'ATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">initial_guess_score</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Score table from an initial guess prediction step. This should be the content of score.sc returned from an Alphafold Initial Guess process. Used to evaluate the quality of initial guess predictions.</td><td style="word-wrap: break-word;">description	pst	rmsd	pae	plddt model_1	0.75	1.9	2.1	85.3 model_2	0.72	2.2	2.4	83.9 </td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">['MOCK', 'PROD', 'TEST']</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined example results, PROD performs the full evaluation, and TEST limits inputs for faster checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">best_folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Mapping from protein name to the selected best folding PDB content.</td><td style="word-wrap: break-word;">{'design_A': 'ATOM ...\nEND\n', 'design_B': 'ATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV table of evaluation metrics for the assessed foldings (e.g., pst, rmsd, pae, plddt).</td><td style="word-wrap: break-word;">name,pst,rmsd,pae,plddt design_A,0.76,1.8,2.0,86.1 design_B,0.70,2.3,2.5,82.4 </td></tr>
</tbody>
</table>
</div>

## Important Notes
- Initial guess score must be provided as a text string (the contents of score.sc), not a file path.
- In TEST mode, only the first structure from generation_pdb and folding_pdb is processed to speed up iteration.
- In MOCK mode, the node returns deterministic example outputs useful for UI/testing; do not use for scientific conclusions.
- Runtime scales with the number of input designs; large batches may take significantly longer.
- Ensure that generation and folding inputs are correctly keyed and correspond to the same designs for meaningful evaluation.

## Troubleshooting
- If score.csv is empty or missing rows, verify that generation_pdb and folding_pdb contain entries and that initial_guess_score is provided.
- If results look incorrect, confirm that the initial_guess_score string is the actual score.sc content and is not truncated or formatted incorrectly.
- When TEST mode seems to ignore many inputs, this is expected: only the first item of each input is evaluated for speed.
- If processing takes too long, reduce the number of designs or switch to TEST mode to validate setup before full runs.
- If you see mismatches in names between best_folding.pdb and score.csv, ensure consistent naming in your input mappings so designs and foldings align.
