# Boltz Multimer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts binder–target multimer protein complexes using Boltz-based diffusion with recycling. Takes MSA inputs for a binder and a single target, optionally focuses on specified target interface residues, and returns ranked complex PDBs with confidence metrics. Deprecated: prefer Boltz2MultimerNode for newer features and continued support.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzmultimernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after generating MSAs for both a binder and a single target sequence. Provide the binder's A3M(s) and exactly one target A3M. Optionally specify a set of target residue indices to guide interface contacts. The node will run multimer prediction and return ranked complex structures and confidence scores for each binder entry.

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
<tr><td style="word-wrap: break-word;">binder_a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Dictionary of binder MSA(s) in A3M format. Keys are sequence IDs; values are A3M contents. Each binder entry will be paired against the single target.</td><td style="word-wrap: break-word;">{"binder_1": "<a3m-content>", "binder_2": "<a3m-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">target_a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Dictionary containing exactly one target MSA in A3M format. The single key is the target sequence ID; the value is its A3M content.</td><td style="word-wrap: break-word;">{"target": "<a3m-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during prediction. Higher values may improve accuracy at the cost of runtime.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples (structure hypotheses) to generate per binder entry. Larger values yield more diversity.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">target_specs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of residue indices on the target chain that define or focus the binding interface.</td><td style="word-wrap: break-word;">1,2,3,45,78</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. Each binder entry increments this seed to ensure varied yet reproducible sampling.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of ranked predicted complex structures. Keys combine binder ID and rank; values are PDB contents.</td><td style="word-wrap: break-word;">{"binder_1_rank_1.pdb": "<pdb-content>", "binder_1_rank_2.pdb": "<pdb-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">confidence_scores.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Dictionary of confidence metrics per predicted structure (e.g., model confidence and related scores).</td><td style="word-wrap: break-word;">{"binder_1_rank_1": {"pLDDT": 85.2, "other_metric": 0.91}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: This node is deprecated. Use Boltz2MultimerNode for improved functionality (constraints, templates, multiple entities, affinity prediction).
- **Single target only**: target_a3m must contain exactly one entry; multiple targets are not supported.
- **Interface residues**: target_specs must be integers (1-based indices) separated by commas; invalid or empty values may degrade or invalidate results.
- **Batch behavior**: All binder entries are paired with the single target; the seed is incremented per binder entry.
- **Runtime considerations**: Higher recycling_steps and diffusion_samples increase compute time.

## Troubleshooting
- **Multiple target entries provided**: If you pass more than one target A3M, the node raises an error. Provide exactly one key-value pair in target_a3m.
- **Invalid target_specs**: If residue indices cannot be parsed as integers, correct the input to a comma-separated list of integers (e.g., 5,12,27).
- **Mismatched or malformed A3M data**: Ensure binder_a3m and target_a3m contain valid A3M text for the intended sequences; regenerate MSAs if necessary.
- **Empty outputs or missing ranks**: Reduce diffusion_samples or recycling_steps to test minimal settings, verify input MSAs are not empty, and retry.
- **Timeouts or long runtimes**: Lower recycling_steps and diffusion_samples; run fewer binder entries per invocation.
