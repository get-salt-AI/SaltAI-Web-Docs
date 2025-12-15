# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a full Boltz structure prediction job using a prepared Boltz YAML configuration and its auxiliary files. It submits the job to the Salt biotech backend and returns predicted structures along with confidence metrics, and optionally affinity metrics when requested in the YAML.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use after assembling your sequences, constraints, templates, and optional properties into a Boltz YAML bundle (typically via Boltz YAML Combiner). Provide that YAML and its auxiliary files to this node, set the random seed and optional inference parameters, then run to obtain predicted PDBs and confidence JSON. If your YAML includes affinity properties and ligand entities, an affinity JSON will also be returned.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Structured configuration describing sequences (proteins/DNA/RNA/ligands), constraints, templates, and optional properties for the Boltz run. Typically produced by Boltz YAML Combiner.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'ACDE...'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the YAML (e.g., MSA files, template PDB/CIF files) packaged as a filename-to-content mapping.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m-contents>', 'template_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed used for the prediction process.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during inference.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many independent diffusion samples to generate.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples processed in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Structure output format.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Worker processes (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to keep when subsampling is enabled.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable inference-time potentials for improved quality (may be slower).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Write the full Predicted Aligned Error (PAE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Write the full Predicted Distance Error (PDE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction for affinity output (only used if affinity is predicted).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps for affinity prediction.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples for affinity prediction.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping generated structure names to PDB (or chosen format) contents for ranked predictions.</td><td style="word-wrap: break-word;">{'ranked_0.pdb': '<pdb-contents>', 'ranked_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics (e.g., per-model scores, optional matrices when enabled).</td><td style="word-wrap: break-word;">{'ranking_confidence': {'ranked_0': 0.78}, 'pae': '<optional-matrix>'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction outputs when affinity is requested in the YAML and ligands are present; empty otherwise.</td><td style="word-wrap: break-word;">{'predicted_affinity': {'complex_0': {'kd': 1.2}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Affinity requirements**: Affinity outputs are only produced when the YAML includes affinity properties and at least one ligand sequence; otherwise affinity.json will be empty.
- **Validation**: The node validates that boltz_yaml is a dictionary including at least one sequence and that boltz_files is a dictionary; invalid inputs raise errors.
- **Performance settings**: Increasing sampling_steps, diffusion_samples, or enabling use_potentials can improve quality at the cost of runtime.
- **Output format**: output_format controls structure serialization (pdb or mmcif).
- **MSA controls**: Use max_msa_seqs, subsample_msa, and num_subsampled_msa to manage memory/runtime for large MSAs.
- **No trajectory output here**: This node does not return diffusion trajectory data; use Boltz Partial Diffusion for trajectories.

## Troubleshooting
- **Error: 'Boltz YAML must be a dictionary'**: Ensure you pass the YAML object produced by Boltz YAML Combiner, not a string or malformed data.
- **Error: 'Boltz YAML must contain at least one sequence'**: Add at least one protein/DNA/RNA/ligand sequence in the YAML.
- **Error: 'Boltz files must be a dictionary'**: Provide auxiliary files as a mapping of filenames to file contents.
- **Error: 'Affinity prediction requires at least one ligand sequence'**: Add a ligand entity to sequences and include appropriate affinity properties in the YAML.
- **Timeouts or long runs**: Reduce sampling_steps or diffusion_samples, disable use_potentials, lower max_msa_seqs, or enable subsample_msa.
- **Empty outputs**: Check that the YAML references auxiliary files by the expected filenames and that boltz_files contains those filenames with valid contents.
