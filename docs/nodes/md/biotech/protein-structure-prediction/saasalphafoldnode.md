# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts protein 3D structures from multiple sequence alignments (A3M) using an Alphafold service. Supports monomer model presets and optional template search, with deterministic seeding and per-sequence batching. Multimer and relaxation are currently not supported.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasalphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating MSAs. Typical workflow: 1) Run MSA Search to produce A3M alignments for your sequences, 2) Feed the resulting A3M dict into Alphafold to obtain ranked PDB structures. Choose TEST mode for quick sanity checks, and PROD for full runs.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Multiple sequence alignments in A3M format, provided as a dict mapping sequence IDs to A3M content. Keys represent sequence IDs; values are the corresponding A3M strings.</td><td style="word-wrap: break-word;">{"seq1": ">seq1\nMKT...\n>homolog1\nMKV...\n", "seq2": ">seq2\nGAA..."}</td></tr>
<tr><td style="word-wrap: break-word;">search_templates</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to perform template search prior to prediction. Can improve accuracy but increases runtime.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Alphafold model configuration to use. Supported: monomer, monomer_ptm, monomer_casp14. The value "multimer" is listed but not supported and will error.</td><td style="word-wrap: break-word;">monomer_ptm</td></tr>
<tr><td style="word-wrap: break-word;">models_to_relax</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When to run relaxation (NONE, BEST, ALL). Currently not supported; must be NONE.</td><td style="word-wrap: break-word;">NONE</td></tr>
<tr><td style="word-wrap: break-word;">enable_gpu_relax</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether relaxation runs on GPU or CPU. Has no effect currently since relaxation is not supported.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">skip_models</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of Alphafold model indices (1–5) to skip during prediction. At least one model must remain. Example: "2,5" to skip models 2 and 5.</td><td style="word-wrap: break-word;">2,4</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. For multiple sequences, each subsequent sequence uses an incremented seed (seed + index).</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Run mode: MOCK (returns predefined results), PROD (calls the service), TEST (uses minimal parameters for quick checks). In TEST mode, template search is disabled and only model 1 is used.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked predicted structures for each input sequence as a dict. Keys are composed of the input sequence ID and model/rank identifiers; values are PDB file contents.</td><td style="word-wrap: break-word;">{"seq1_model_1_rank_1": "ATOM ...", "seq1_model_1_rank_2": "ATOM ..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model limitations**: Multimer model is not supported and will raise an error if selected.
- **Relaxation unsupported**: models_to_relax must be NONE; BEST and ALL are not currently available.
- **skip_models validation**: Values must be integers 1–5 and cannot skip all five models; at least one model must run.
- **TEST mode behavior**: Automatically disables template search and runs only model 1 (equivalent to skipping models 2–5).
- **Input consistency**: The A3M dict keys (sequence IDs) must match the extracted FASTA IDs; mismatches will error.
- **Seeding per sequence**: For multiple sequences, the seed used is incremented per sequence (seed + index), aiding reproducibility.
- **Runtime considerations**: Longer sequences and enabling template search increase runtime and compute requirements.

## Troubleshooting
- **Error: Multimer model is not supported**: Set model_preset to a supported monomer variant (monomer, monomer_ptm, or monomer_casp14).
- **Error: Relaxation is not supported**: Ensure models_to_relax is set to NONE.
- **Error: Expected models indices in `skip_models` to be in range [1, 5]**: Provide a comma-separated list with values from 1 to 5 only.
- **Error: Cannot skip all 5 models**: Remove at least one index from skip_models so at least one model runs.
- **Error: Expected FASTA IDs to match features IDs**: Ensure A3M keys correctly correspond to the sequence IDs; do not rename keys between steps.
- **Unexpectedly fast results in MOCK mode**: Verify mode is set to PROD for real predictions.
- **Long queue or timeout feelings**: Reduce sequence length, disable template search, or use TEST mode to validate pipeline quickly before full runs.
