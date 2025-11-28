# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Boltz structure prediction job from a prepared Boltz YAML configuration and auxiliary files. It supports tuning diffusion and sampling parameters, optional MSA handling controls, and can optionally compute ligand-binding affinity if requested in properties.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use after assembling your system with Boltz Sequence/Constraint/Template/Property nodes and combining them via Boltz List Combiner and Boltz YAML Combiner. Connect the resulting boltz_yaml and boltz_files here, set your seed and optional parameters, then run to obtain predicted structures (as PDB or mmCIF), model confidence metrics, and affinity results if requested.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Complete Boltz YAML configuration describing sequences, optional constraints, templates, and properties. Must include at least one sequence.</td><td style="word-wrap: break-word;">{ "version": 1, "sequences": [ { "protein": { "id": "A", "sequence": "..." } } ], "properties": [ { "affinity": { "binder": "L" } } ] }</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary file map referenced by the YAML (e.g., MSA .a3m and template .pdb files). Keys are filenames referenced in YAML; values are file contents.</td><td style="word-wrap: break-word;">{ "msa_1.a3m": "...", "template_1.pdb": "...PDB text..." }</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during structure refinement.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps for the diffusion process.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many independent diffusion samples to generate.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples processed in parallel (resource-dependent).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like factor).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Structure output format: pdb or mmcif.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences after subsampling (if enabled).</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable inference-time potentials for improved quality (may be slower).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Write the full Predicted Aligned Error (PAE) matrix to outputs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Write the full Predicted Distance Error (PDE) matrix to outputs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction to affinity estimates (used only if affinity is requested in properties).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps for affinity estimation (used only if affinity is requested).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples for affinity estimation (used only if affinity is requested).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping structure filenames to PDB (or mmCIF) content for ranked predictions.</td><td style="word-wrap: break-word;">{ "rank1.pdb": "...PDB text...", "rank2.pdb": "..." }</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Dictionary of confidence-related outputs per sample (e.g., pLDDT/PAE data or filenames to JSON).</td><td style="word-wrap: break-word;">{ "rank1_confidence.json": "{...}", "rank2_confidence.json": "{...}" }</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Dictionary of affinity results when affinity is requested in properties; empty if not applicable.</td><td style="word-wrap: break-word;">{ "rank1_affinity.json": "{...}" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Affinity requires a ligand**: If properties include affinity, at least one ligand sequence must be present; otherwise the node raises an error.
- **YAML and files must align**: Any filenames referenced in the YAML (e.g., MSA or template entries) must exist in boltz_files with matching keys.
- **Output format**: Structures are emitted in the selected format (pdb or mmcif), but the field name remains structures.pdb; use the contents accordingly.
- **Parallelism and resources**: Increasing max_parallel_samples and num_workers increases resource usage and may exhaust memory/compute on limited systems.
- **MSA controls**: You can cap or subsample MSAs via max_msa_seqs, subsample_msa, and num_subsampled_msa to manage speed and memory.
- **Reproducibility**: The seed is applied, but multiple diffusion samples and parallelism can still introduce variability across runs.

## Troubleshooting
- **Error: 'Boltz YAML must contain at least one sequence'**: Ensure you connected a valid boltz_yaml from Boltz YAML Combiner that includes the sequences list.
- **Error: 'Affinity prediction requires at least one ligand sequence'**: Remove affinity properties or add a ligand sequence and binder chain.
- **Empty or missing outputs**: Verify sampling_steps and diffusion_samples are reasonable; check that boltz_files include required MSA/template files referenced by YAML.
- **High memory or long runtimes**: Reduce max_parallel_samples, num_workers, sampling_steps, or MSA sizes; disable use_potentials if not needed.
- **Invalid file references**: If you see key errors for MSA/template files, confirm the YAML references filenames that exist as keys in boltz_files.
- **Unexpected structure format**: If you chose mmcif output but expected PDB, set output_format to "pdb" or adapt downstream consumers to handle mmCIF.
