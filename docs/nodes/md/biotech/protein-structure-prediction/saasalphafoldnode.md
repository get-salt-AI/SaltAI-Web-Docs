# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein structure prediction from MSA (A3M) alignments and returns ranked PDB models. Supports single-chain (monomer) presets and deterministic seeding per sequence. TEST and MOCK modes are available for quick checks or offline examples.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasalphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use after generating MSAs with an MSA search node. Provide the A3M alignments, choose the Alphafold model preset, optionally disable specific model indices, and run to obtain ranked PDB structures. Typical workflow: MSA Search -> Alphafold -> downstream analysis or visualization.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Dictionary of MSA results in A3M format, keyed by sequence ID. Each value is the A3M text for that sequence.</td><td style="word-wrap: break-word;">{'seq1': '>query/1-100\\nACDEFG...\\n>hit1\\nAC-EFG...', 'seq2': '>query/1-85\\nMKTA...\\n>hit1\\nMK-A...'}</td></tr>
<tr><td style="word-wrap: break-word;">search_templates</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to search for structural templates prior to prediction.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Alphafold model preset to use. Options: monomer, monomer_ptm, monomer_casp14, multimer. Multimer is currently not supported.</td><td style="word-wrap: break-word;">monomer_ptm</td></tr>
<tr><td style="word-wrap: break-word;">models_to_relax</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Controls relaxation of predicted models. Options: NONE, BEST, ALL. Relaxation is not supported yet; must be NONE.</td><td style="word-wrap: break-word;">NONE</td></tr>
<tr><td style="word-wrap: break-word;">enable_gpu_relax</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to run relaxation on GPU or CPU (effective only if relaxation were supported).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">skip_models</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated indices of Alphafold models to skip (1-5). Cannot skip all five. Example: "3,5".</td><td style="word-wrap: break-word;">2,5</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. If multiple sequences are processed, each sequence uses seed + index to ensure reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK uses bundled example outputs, PROD runs full prediction, TEST reduces compute by limiting models and disabling templates.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of ranked PDB structures keyed by '<sequence_id>_<ranked_model_id>'. Each value is PDB file content as text.</td><td style="word-wrap: break-word;">{'seq1_rank_1': 'HEADER ...\\nATOM ...\\nEND', 'seq1_rank_2': 'HEADER ...\\nATOM ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unsupported features**: Multimer preset and relaxation (models_to_relax other than NONE) are not supported and will raise errors.
- **Skip models validation**: Indices must be in [1,5] and you cannot skip all five models.
- **TEST mode behavior**: Forces skipping models 2-5 and disables template search to speed up runs.
- **Input consistency**: The A3M dictionary keys define sequence IDs; internal processing requires consistent IDs across generated features.
- **Seeding**: For multiple sequences, the node increments the base seed per sequence (seed + sequence_index) for reproducibility.
- **MOCK mode**: Returns predefined example results for demonstration without running prediction.

## Troubleshooting
- **Error: Multimer model not supported**: Set model_preset to a monomer option (e.g., monomer or monomer_ptm).
- **Error: Relaxation not supported**: Set models_to_relax to NONE.
- **Error: Invalid skip_models**: Ensure values are comma-separated integers within 1-5 and do not include all five indices.
- **No or mismatched outputs**: Verify that input A3M keys are correct and correspond to intended sequence IDs; ensure A3M content is valid.
- **Unexpected TEST results**: Remember TEST mode restricts to a single model and disables templates; switch to PROD for full predictions.
- **Empty or low-quality structures**: Check MSA quality and coverage. Re-run MSA search with appropriate databases before predicting.
