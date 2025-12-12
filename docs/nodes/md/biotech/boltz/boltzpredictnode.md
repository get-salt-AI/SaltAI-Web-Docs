# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Boltz structure prediction job from a prepared Boltz YAML configuration and its auxiliary files. It supports configurable sampling, recycling, diffusion, and output controls, and can optionally compute ligand-binding affinity when requested in the YAML.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building sequences, constraints, templates, and properties with the Boltz builder nodes and combining them into a Boltz YAML and files. Connect the Boltz YAML and files here, choose sampling and runtime options, then execute to obtain ranked structures and confidence metrics. Include an affinity property and at least one ligand in the YAML if you want affinity predictions.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz YAML configuration that defines sequences (and optionally constraints, templates, and properties). Typically produced by Boltz YAML Combiner.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'AAAA'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the Boltz YAML (e.g., MSA and template files) as a mapping of filename to file content. Typically produced by Boltz YAML Combiner.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m-content>', 'template_1.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations used during prediction.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many independent diffusion samples to generate.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to process concurrently.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">["pdb", "mmcif"]</td><td style="word-wrap: break-word;">Format for produced structure files.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Worker processes to use. Set 0 to disable multiprocessing.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences considered.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to use if subsampling is enabled.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable inference-time potentials (improves quality at additional compute cost).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full Predicted Aligned Error (PAE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full Predicted Distance Error (PDE) matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction when computing affinity (only used if affinity is requested in properties).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps used specifically for affinity prediction.</td><td style="word-wrap: break-word;">200</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A mapping of ranked structure filenames to file content. The file extensions reflect the selected output_format.</td><td style="word-wrap: break-word;">{'ranked_0.pdb': '<pdb-content>', 'ranked_1.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A mapping of confidence metric filenames to their JSON content (e.g., pLDDT/PAE summaries).</td><td style="word-wrap: break-word;">{'confidence.json': '{"plddt_mean": 82.1}'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A mapping of affinity result filenames to JSON content. Present only if affinity was requested in properties and at least one ligand sequence is included; otherwise returned as an empty mapping.</td><td style="word-wrap: break-word;">{'affinity.json': '{"predicted_Kd_nM": 120.5}'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Affinity requirements**: Affinity prediction is only run when the YAML includes an affinity property and at least one ligand sequence; otherwise the affinity output will be empty.
- **Input validation**: boltz_yaml must include at least one sequence; boltz_files must be a dictionary mapping referenced filenames to file content.
- **MSA/templates handling**: Use Boltz YAML Combiner to automatically generate correct auxiliary filenames and mappings from provided MSA and template inputs.
- **Output format**: The output_format setting controls the file suffixes inside the structures mapping; the structural output remains a file mapping regardless of selected format.
- **Performance controls**: num_workers=0 disables multiprocessing. Increasing diffusion/recycling steps improves quality but increases runtime.
- **Determinism**: The seed controls stochastic components of sampling; multiple diffusion_samples may still introduce variability between samples.

## Troubleshooting
- **Error: Affinity requested but no ligand provided**: Add a ligand sequence to the YAML or remove the affinity property.
- **Error: Boltz YAML must contain at least one sequence**: Ensure your sequences are built and connected into the YAML via the combiner.
- **Referenced MSA/template file not found**: Confirm that MSAs/templates were passed into the combiner so it creates filenames and populates boltz_files correctly.
- **Empty or missing outputs**: If structures are empty, reduce sampling_steps or diffusion_samples to test configuration, verify sequences and constraints are valid, and check that input files have non-empty content.
- **Runtime too long or timeouts**: Decrease diffusion_samples, sampling_steps, or max_parallel_samples; set num_workers appropriately for available resources.
- **Invalid parameter ranges**: Ensure integers and floats are within allowed ranges (e.g., sampling_steps between 50 and 500).
