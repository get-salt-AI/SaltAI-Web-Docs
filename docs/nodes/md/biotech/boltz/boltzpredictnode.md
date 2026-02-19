# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Boltz structure prediction from a prepared Boltz YAML and its auxiliary files. It produces ranked 3D structures plus associated confidence and scoring data. If affinity prediction is requested and valid ligand inputs are present, it also returns affinity estimates. PAE, PDE, and pLDDT matrices are returned as JSON-decoded arrays.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling inputs with Boltz Sequence/Constraint/Template/Property builders and combining them with Boltz List Combiner and Boltz YAML Combiner. Connect the resulting boltz_yaml and boltz_files here, set prediction parameters (steps, samples, format), and run to obtain structures and confidence/affinity outputs. Choose output_format to get PDB or mmCIF files.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">The Boltz configuration produced by Boltz YAML Combiner. Must contain at least one valid sequence entry.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'MKT...', '_sequence_name': 'seqA'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the YAML (e.g., MSA files, templates) produced by Boltz YAML Combiner.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m file content>', 'template_1.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling steps during prediction.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps for the main diffusion process.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many independent samples to generate.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples processed in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">CHOICE['pdb','mmcif']</td><td style="word-wrap: break-word;">Format of the output structures. When 'mmcif' is chosen, structure file names will use .cif extension.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to consider.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to keep when subsampling is enabled.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable inference-time potentials to improve quality (may be slower).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, write full PAE matrices to outputs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, write full PDE matrices to outputs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction when computing affinity (only relevant if affinity is requested).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps to use for affinity prediction runs.</td><td style="word-wrap: break-word;">200</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked structure files keyed by filename. If output_format is 'mmcif', keys use .cif extension.</td><td style="word-wrap: break-word;">{'rank_001_model_1.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Per-structure confidence data (e.g., scores/metrics) keyed by filename.</td><td style="word-wrap: break-word;">{'confidence_rank_001.json': {'iptm': 0.78, 'ptm': 0.65}}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction results (only populated if affinity was requested and ligands are present).</td><td style="word-wrap: break-word;">{'affinity_rank_001.json': {'kd': 1.2e-07, 'units': 'M'}}</td></tr>
<tr><td style="word-wrap: break-word;">pae.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Predicted Aligned Error matrices per model, decoded from NPZ to JSON-serializable arrays.</td><td style="word-wrap: break-word;">{'pae_rank_001': {'predicted_aligned_error': [[0.0, 1.2], [1.1, 0.0]]}}</td></tr>
<tr><td style="word-wrap: break-word;">pde.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Predicted Distance Error matrices per model, decoded from NPZ to JSON-serializable arrays.</td><td style="word-wrap: break-word;">{'pde_rank_001': {'predicted_distance_error': [[0.0, 0.9], [0.8, 0.0]]}}</td></tr>
<tr><td style="word-wrap: break-word;">plddt.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Per-residue pLDDT scores per model, decoded from NPZ to JSON-serializable arrays.</td><td style="word-wrap: break-word;">{'plddt_rank_001': {'plddt': [92.1, 88.4, 85.0]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Affinity outputs are only produced if the YAML includes at least one ligand sequence and an affinity property; otherwise affinity.json will be empty.
- When output_format is set to 'mmcif', the structure result keys are renamed to use .cif extensions.
- Boltz YAML and files must be consistent: any referenced MSA/template in the YAML must exist in boltz_files.
- Large sampling_steps, diffusion_samples, or enabling potentials can significantly increase runtime and resource usage.
- MSA subsampling parameters only affect runs where MSA content is provided via the YAML.

## Troubleshooting
- Error: 'Boltz YAML must contain at least one sequence' — Ensure the YAML from Boltz YAML Combiner includes a valid sequences list.
- Error: 'Boltz files must be a dictionary' — Connect the boltz_files output from Boltz YAML Combiner directly.
- Error: 'Affinity prediction requires at least one ligand sequence' — Add a ligand sequence and include an affinity property when requesting affinity.
- Empty or missing PAE/PDE/pLDDT arrays — These matrices are decoded from NPZ; if the backend did not produce them or decoding failed, outputs may be empty.
- Unexpected structure file extensions — Verify output_format. Select 'pdb' for .pdb or 'mmcif' for .cif naming.
- Slow execution or timeouts — Reduce diffusion_samples or sampling_steps, disable use_potentials, lower max_parallel_samples, or decrease num_workers if resource-constrained.
