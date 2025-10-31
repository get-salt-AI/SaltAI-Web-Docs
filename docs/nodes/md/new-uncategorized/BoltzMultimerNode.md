# Boltz Multimer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts the 3D structure of a protein complex (binder + target) using Boltz multimer modeling. It takes MSA inputs for both partners, optionally constrains the interface via residue indices, and returns ranked PDB structures with confidence metrics. Deprecated: Use Boltz2 Multimer for enhanced features and future support.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/BoltzMultimerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have separate MSA results (A3M) for a binder protein and a single target protein and want to generate complexed structures. Typical workflow: perform MSA searches for binder and target, feed both A3Ms here, optionally provide interface residues (target_specs) to guide binding, then consume the returned PDBs and confidence scores for downstream selection or analysis.

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
<tr><td style="word-wrap: break-word;">binder_a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA search results for the binder protein. Provide as a dictionary {sequence_id: a3m_content}. Each binder sequence will be paired with the single target to form a complex.</td><td style="word-wrap: break-word;">{"binder_seq_1": ">seq\nAAAA...\n>...\n"}</td></tr>
<tr><td style="word-wrap: break-word;">target_a3m</td><td>True</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">MSA search results for the target protein. Must contain exactly one entry: {target_id: a3m_content}.</td><td style="word-wrap: break-word;">{"target_seq": ">seq\nBBBB...\n>...\n"}</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations used during prediction. Higher values can improve accuracy but increase compute time.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many diverse structure samples to generate via diffusion. More samples increase diversity and runtime.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">target_specs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated residue indices on the target chain to define or bias the binding interface. Leave empty for no constraints.</td><td style="word-wrap: break-word;">1,2,3,45,76</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility. If multiple binder entries are provided, each subsequent one uses an incremented seed.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">folding.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked complex structures. Returned as a dictionary mapping identifiers to PDB text. Keys combine the binder sequence ID and a model rank identifier.</td><td style="word-wrap: break-word;">{"binder_seq_1_rank_1": "ATOM ...\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">confidence_scores.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence and quality metrics for each generated structure (e.g., per-model scores). Keys align with the structure identifiers.</td><td style="word-wrap: break-word;">{"binder_seq_1_rank_1": {"score": 0.78, "metrics": {"plddt": 72.3}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: This node is deprecated in favor of Boltz2 Multimer, which adds constraints, templates, multiple entities, and affinity prediction.
- **Single target required**: Only one target A3M is supported. Supplying multiple targets will cause an error.
- **Input format**: Both binder_a3m and target_a3m should be dictionaries of A3M contents keyed by sequence IDs.
- **Interface specification**: target_specs must be a comma-separated list of integers (residue indices on the target). Leave empty to run unconstrained.
- **Runtime vs quality**: Increasing recycling_steps and diffusion_samples improves thoroughness and diversity but increases runtime.
- **Seeding behavior**: The seed is incremented per binder entry to ensure unique sampling across multiple binders.

## Troubleshooting
- **Multiple targets error**: If you see an error about unsupported multiple target A3Ms, ensure target_a3m contains exactly one entry.
- **Invalid target_specs**: If parsing fails, verify target_specs is either empty or a comma-separated list of integers (e.g., 5,12,29).
- **Missing outputs or empty results**: Check that A3M inputs are non-empty and properly keyed. Ensure binder and target sequences are valid and not excessively short.
- **Long runtimes**: Reduce recycling_steps and diffusion_samples to shorten execution time.
- **Identifier mismatches**: If downstream steps cannot match outputs, confirm that you are using the returned keys from folding.pdb and confidence_scores.json.
- **Low confidence models**: Increase diffusion_samples to explore more structures, or refine constraints via target_specs if appropriate.
