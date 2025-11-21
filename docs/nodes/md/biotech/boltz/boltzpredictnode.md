# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Boltz structure prediction job from a prepared Boltz YAML plus its auxiliary files. It validates inputs, submits a prediction request to the Boltz service, and returns ranked structure files with associated confidence metrics, and optionally affinity predictions when requested. Output may include multiple ranked structures aggregated into a dictionary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling sequences, constraints, templates, and optional properties with the Boltz YAML Combiner. Connect the resulting boltz_yaml and boltz_files here, set a seed and optional inference parameters, then execute to obtain predicted structures and confidence JSON. Include an affinity property and at least one ligand sequence in the YAML to get affinity outputs.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz configuration produced by BoltzYAMLCombinerNode. Must include at least one sequence and valid chain IDs.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'MSEQN...'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the YAML (e.g., MSA and template files). Keys are filenames; values are their file contents.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m-contents>', 'template_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during prediction (1–20).</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps (50–500). Higher values can improve quality with more compute.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of independent diffusion samples to generate per prediction (1–25).</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to compute in parallel (1–10).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter, 1.0–2.0).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">[pdb, mmcif]</td><td style="word-wrap: break-word;">Desired output structure file format.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use (256–16384).</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences before use.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to keep when subsampling (256–4096).</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable inference-time potentials for improved quality (slower).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, write full Predicted Aligned Error (PAE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, write full Predicted Distance Error (PDE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction when computing affinity (only used if affinity is requested).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps used for affinity calculation (50–500).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples used for affinity prediction (1–10).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping result names to predicted structure contents in the chosen format (PDB or mmCIF). Keys typically include ranked file names.</td><td style="word-wrap: break-word;">{'sequence_0_ranked_0.pdb': '<pdb-contents>', 'sequence_0_ranked_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Dictionary of confidence-related outputs (e.g., pLDDT, PAE summaries) keyed by file name.</td><td style="word-wrap: break-word;">{'sequence_0_confidence.json': '{"plddt": [...], "pae": ...}'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Dictionary of affinity prediction outputs when requested; empty if no affinity property was included.</td><td style="word-wrap: break-word;">{'sequence_0_affinity.json': '{"kd": 1.23, "units": "uM"}'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Affinity requires a ligand**: If the YAML includes an affinity property, at least one ligand sequence must be present; otherwise the node raises an error.
- **Input validation**: boltz_yaml must be a dictionary containing at least one sequence; boltz_files must be a dictionary of auxiliary file contents.
- **Multiple outputs**: The node may return multiple ranked structures and associated metrics as a dictionary of name->content.
- **Output format**: Although the output field is named structures.pdb, the actual structure content respects the output_format setting (pdb or mmcif).
- **Performance settings**: Increasing sampling_steps, diffusion_samples, and enabling use_potentials can improve quality at higher compute cost.
- **MSA handling**: MSA file preparation and de-duplication are handled upstream by the Boltz YAML Combiner; ensure those inputs are correctly wired.
- **Reproducibility**: The seed controls stochastic parts of the process; keep it fixed to reproduce results, adjusting only when intentionally exploring variability.
- **Execution**: The node submits work to the Boltz prediction service and waits up to a set timeout server-side.

## Troubleshooting
- **Error: Affinity prediction requires at least one ligand sequence**: Add a ligand sequence to the YAML and ensure a property of type affinity is included.
- **Error: Boltz YAML must contain at least one sequence**: Verify the upstream combiner produced a non-empty sequences list.
- **Error: Boltz files must be a dictionary**: Connect the boltz_files output from Boltz YAML Combiner directly; do not pass a list or string.
- **Empty affinity.json output**: This is expected if no affinity property was provided in the YAML; add it via the property builder if needed.
- **Unexpected structure format**: If you requested mmcif but see a .pdb-like key name, check that output_format is correctly set; the content adheres to the format even if the display name is generic.
- **Slow runtime**: Reduce diffusion_samples, sampling_steps, or disable use_potentials; also consider lowering max_parallel_samples or num_workers depending on resource limits.
- **Invalid chain IDs or missing files**: Ensure all referenced chain IDs and auxiliary file names in boltz_yaml match those provided in boltz_files.
