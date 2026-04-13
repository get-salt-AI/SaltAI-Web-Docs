# Alphafold

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs protein structure prediction from multiple sequence alignment (MSA, A3M format) and returns ranked PDB models. Supports single-chain (monomer) presets and deterministic seeding per sequence, with TEST and MOCK modes for fast validation or offline/example runs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-structure-prediction/saasalphafoldnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating MSAs with an upstream MSA search node. Provide the A3M alignments, choose the Alphafold model preset, optionally configure model indices and deterministic seeds, then run to obtain ranked PDB structures for downstream analysis, filtering, or visualization.

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
<tr><td style="word-wrap: break-word;">msa_a3m</td><td>True</td><td style="word-wrap: break-word;">MSA_A3M</td><td style="word-wrap: break-word;">Multiple sequence alignment data in A3M format, typically produced by an MSA search node. Encodes the evolutionary information Alphafold uses for structure prediction.</td><td style="word-wrap: break-word;">>query/1-120\nACDEFGHIKLMNPQRSTVWY...\n>homolog1\nAC-EFGHIKLMNPQRSTVWY...</td></tr>
<tr><td style="word-wrap: break-word;">model_preset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Alphafold model preset used to configure single-chain (monomer) or related inference settings, controlling how the prediction is run.</td><td style="word-wrap: break-word;">monomer</td></tr>
<tr><td style="word-wrap: break-word;">disabled_model_indices</td><td>False</td><td style="word-wrap: break-word;">STRING_LIST</td><td style="word-wrap: break-word;">Optional list of model indices to disable so that only selected Alphafold models contribute to prediction and ranking.</td><td style="word-wrap: break-word;">['1', '3']</td></tr>
<tr><td style="word-wrap: break-word;">deterministic_seed</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Optional random seed applied per sequence to make predictions reproducible when inputs and settings are unchanged.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode for the node. Normal mode runs full Alphafold; TEST and MOCK modes can be used for quick checks or offline/example behavior, potentially with reduced or synthetic outputs.</td><td style="word-wrap: break-word;">TEST</td></tr>
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
<tr><td style="word-wrap: break-word;">pdb_models</td><td style="word-wrap: break-word;">PDB_MODELS</td><td style="word-wrap: break-word;">Ranked three-dimensional protein structure models in a PDB-style bundle, usually containing several ranked candidates plus associated confidence information.</td><td style="word-wrap: break-word;">Bundle containing ranked_0.pdb, ranked_1.pdb, ranked_2.pdb, each with per-residue confidence scores.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input dependency**: The node assumes that valid A3M MSAs are already generated (for example by an MSA search node); it does not perform sequence search or alignment.
- **Preset focus**: Documented usage focuses on single-chain (monomer) presets; availability or behavior of other presets may depend on your specific environment.
- **Reproducibility**: Use deterministic_seed to obtain consistent predictions across runs, provided the same MSA and settings are used.
- **Lightweight modes**: TEST and MOCK modes are for validation or demonstration and may skip heavy computation or return simplified/example structures.
- **Resource use**: Full Alphafold inference is compute-intensive and may be slow for long sequences or large MSAs; consider validating pipelines in TEST mode first.

## Troubleshooting
- **Issue: No or empty output models**: Check that msa_a3m is non-empty, well-formed A3M, and matches the intended query sequence; verify that model_preset is a supported value.
- **Issue: Results differ between runs**: Set deterministic_seed to a fixed integer and avoid changes to msa_a3m, model_preset, or disabled_model_indices between runs.
- **Issue: Very long runtimes**: Inspect MSA depth and sequence length; reduce redundant sequences, shorten the target region, or first run in TEST mode to confirm configuration.
- **Issue: Poor model quality**: Ensure the MSA has sufficient depth and diversity, and avoid disabling too many model indices; consider regenerating the MSA with broader search settings.
- **Issue: Downstream nodes reject output**: Confirm that downstream nodes accept PDB_MODELS as input and are compatible with TEST or MOCK outputs if those modes are used.
