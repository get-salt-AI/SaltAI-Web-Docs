# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs AlphaFold protein structure prediction from MSA inputs in A3M format. It converts A3M to Stockholm, prepares features, and performs model inference to produce ranked PDB structures per input sequence. Supports MOCK, PROD, and TEST modes; currently, multimer models and relaxation are not supported.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/alphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after obtaining multiple sequence alignments (MSAs). A typical workflow is: provide sequences -> run MSA search -> feed the resulting A3M dict into Alphafold. Choose the model preset and whether to search templates (monomer-only). In TEST mode, it speeds up runs by only evaluating the first model and disabling template search. The output is a dictionary of ranked PDB strings keyed by combined sequence ID and rank label.

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
<tr><td style="word-wrap: break-word;">a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Dictionary mapping sequence IDs to A3M MSA contents. Keys are sequence IDs; values are A3M strings for each sequence.</td><td style="word-wrap: break-word;">{"seq1": ">seq1\nMKT...\n>seq1_homolog1\nM-T...\n", "seq2": ">seq2\nAGV..."}</td></tr>
<tr><td style="word-wrap: break-word;">search_templates</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to search structural templates before prediction. Ignored in TEST mode (forced off).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: monomer \| monomer_ptm \| monomer_casp14 \| multimer)</td><td style="word-wrap: break-word;">AlphaFold model variant to use. Multimer is currently not supported.</td><td style="word-wrap: break-word;">monomer</td></tr>
<tr><td style="word-wrap: break-word;">models_to_relax</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: NONE \| BEST \| ALL)</td><td style="word-wrap: break-word;">Selects whether to run relaxation. Currently not supported; must be NONE.</td><td style="word-wrap: break-word;">NONE</td></tr>
<tr><td style="word-wrap: break-word;">enable_gpu_relax</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If relaxation were enabled, choose GPU vs CPU. Currently not used since relaxation is unsupported.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">skip_models</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated model indices (1–5) to skip during inference (e.g., "2,4"). Cannot include all five. Validated at runtime.</td><td style="word-wrap: break-word;">2,4</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. If multiple sequences are present, each subsequent sample uses seed + index.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: MOCK \| PROD \| TEST)</td><td style="word-wrap: break-word;">Execution mode: MOCK uses bundled mock results, PROD runs the actual services, TEST uses quick settings (first model only, no templates) for short sequences.</td><td style="word-wrap: break-word;">PROD</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of ranked PDB predictions per input sequence. Keys are combined as {sequence_id}_{rank_label}; values are PDB contents.</td><td style="word-wrap: break-word;">{"seq1_ranked_0.pdb": "ATOM ...", "seq1_ranked_1.pdb": "ATOM ..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Relaxation is not supported. models_to_relax must be set to NONE.
- Multimer preset is not supported. Choose a monomer preset.
- skip_models must contain only integers 1–5 and cannot include all five; otherwise, the node raises an error.
- In TEST mode, the node automatically disables template search and evaluates only the first model to speed up execution.
- The input A3M dictionary keys (sequence IDs) must remain consistent through processing; mismatched IDs between derived FASTA and features will cause validation errors.

## Troubleshooting
- Error: Expected models indices in skip_models to be in range [1, 5]. Resolution: Provide a comma-separated list using only 1–5 (e.g., 2,4) and ensure no spaces or invalid characters.
- Error: Cannot skip all 5 models. Resolution: Remove at least one index from skip_models.
- Error: Relaxation is not supported yet. Resolution: Set models_to_relax to NONE.
- Error: Multimer model is not supported yet. Resolution: Choose monomer, monomer_ptm, or monomer_casp14.
- Error: Expected FASTA IDs to match features IDs. Resolution: Ensure the a3m input is a dict keyed by the correct sequence IDs and that each value is valid A3M content for that sequence.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-structure-prediction/alphafoldnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

