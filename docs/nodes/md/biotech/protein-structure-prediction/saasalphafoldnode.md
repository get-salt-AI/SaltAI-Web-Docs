# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts 3D protein structures from multiple sequence alignments (MSA) produced earlier in the workflow. It converts A3M MSAs to features and runs the Alphafold model to generate ranked PDB structures. Supports mock/test modes for fast iteration and seeding for reproducibility.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasalphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining MSA search results (A3M) for one or more protein sequences. Provide the A3M dictionary keyed by sequence IDs, choose the Alphafold model preset, optionally toggle template search, set which model indices to skip, and run. The output is a dictionary of ranked PDB strings keyed as '<sequence_id>_<ranked_pdb_name>'.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA search results in A3M format as a dictionary mapping sequence IDs to A3M content. Each key should uniquely identify a sequence; each value is the A3M alignment for that sequence.</td><td style="word-wrap: break-word;">{"seq1": "<a3m_content_for_seq1>", "seq2": "<a3m_content_for_seq2>"}</td></tr>
<tr><td style="word-wrap: break-word;">search_templates</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to search for structural templates before prediction. In TEST mode this is automatically disabled.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">["monomer", "monomer_ptm", "monomer_casp14", "multimer"]</td><td style="word-wrap: break-word;">Select which Alphafold model preset to run. 'multimer' is not supported.</td><td style="word-wrap: break-word;">monomer</td></tr>
<tr><td style="word-wrap: break-word;">models_to_relax</td><td>True</td><td style="word-wrap: break-word;">["NONE", "BEST", "ALL"]</td><td style="word-wrap: break-word;">Whether to run relaxation on results. Relaxation is not supported yet; only 'NONE' is allowed.</td><td style="word-wrap: break-word;">NONE</td></tr>
<tr><td style="word-wrap: break-word;">enable_gpu_relax</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to use GPU for relaxation if relaxation were enabled. Currently has no effect because relaxation is not supported.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">skip_models</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of model indices (1–5) to skip during prediction (e.g., "3,5"). You cannot skip all five models. In TEST mode, this is overridden to skip [2,3,4,5].</td><td style="word-wrap: break-word;">2,4</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed (0 to 4294967295). For multiple sequences, each subsequent sequence uses seed + index to ensure deterministic variation per sample.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">["MOCK", "PROD", "TEST"]</td><td style="word-wrap: break-word;">Execution mode. MOCK returns predefined data, PROD runs full prediction, TEST runs a fast prediction with minimal settings (disables templates and skips all but the first model).</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping '<sequence_id>_<ranked_pdb_name>' to PDB file content (string) for each predicted ranking.</td><td style="word-wrap: break-word;">{"seq1_rank_1": "ATOM ...", "seq1_rank_2": "ATOM ..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model support**: 'multimer' preset is not supported.
- **Relaxation**: Relaxation is not supported yet; set models_to_relax to 'NONE'.
- **skip_models**: Allowed indices are 1–5; you cannot skip all five models. Use a comma-separated string (e.g., "1,3").
- **TEST mode overrides**: In TEST mode, template search is disabled and skip_models is set to [2,3,4,5] regardless of input.
- **Seeding behavior**: For multiple sequences, the effective seed per sequence is seed + sequence_index, enabling reproducible but distinct results per sequence.
- **Input format**: The A3M input must be a dict where keys (sequence IDs) map to A3M content; these IDs are used to build output names and to match features internally.

## Troubleshooting
- **Error: 'multimer model is not supported'**: Choose a supported model_preset (monomer, monomer_ptm, or monomer_casp14).
- **Error: 'Relaxation is not supported'**: Set models_to_relax to 'NONE'.
- **Error: 'Expected models indices in skip_models to be in range [1, 5]'**: Ensure skip_models contains only numbers 1–5 (e.g., "2,5").
- **Error: 'Cannot skip all 5 models'**: Remove at least one index from skip_models.
- **Unexpected output naming or missing results**: Verify the A3M input dictionary keys are correct and unique; each key becomes a prefix in output PDB names.
- **Results differ despite same seed**: Remember seed increments per sequence (seed + i). For single-sequence reproducibility, keep inputs identical and process one sequence.
