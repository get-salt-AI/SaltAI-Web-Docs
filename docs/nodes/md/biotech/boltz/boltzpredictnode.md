# Boltz Predict

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Boltz Predict executes a Boltz2 GPU job based on a validated Boltz YAML configuration and its auxiliary MSA/template files. It sends the configuration to the Boltz Boltz2 service, manages runtime parameters such as sampling, diffusion, and MSA handling, and returns ranked structures plus confidence and optional affinity outputs. It also decodes PAE, PDE, and pLDDT matrices from NPZ into JSON-serializable form for downstream analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpredictnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node as the final execution step in a Boltz-based protein design or structure prediction workflow after you have constructed Boltz YAML and auxiliary files. Typically, you first build sequences, ligands, constraints, templates, and properties with nodes such as BoltzSequenceNode, BoltzProteinSequenceNode, BoltzLigandSequenceNode, BoltzConstraintNode, BoltzTemplateNode, and BoltzPropertyNode. Then you combine them using BoltzListCombinerNode and BoltzYAMLCombinerNode, and finally feed boltz_yaml and boltz_files into Boltz Predict. It dispatches the computation to the biotech Boltz2 GPU backend and returns multiple outputs: ranked structures (PDB or mmCIF), confidence scores, optional affinity predictions (if requested via properties and ligands), and decoded PAE/PDE/pLDDT matrices in JSON for visualization or further scoring. Place this node near the end of your pipeline, and connect its outputs to structural analyzers, visualization exporters, or scoring/filtering nodes. For affinity workflows, ensure your YAML includes at least one ligand sequence and an affinity property, otherwise affinity outputs will be empty or validation will fail. Use sampling and diffusion controls (for example diffusion_samples, sampling_steps, step_scale) to trade off runtime versus exploration, and use max_parallel_samples and num_workers to manage backend resource usage.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Structured Boltz YAML configuration describing sequences, ligands, constraints, templates, and properties. Must be a dictionary with at least a non-empty sequences list, typically constructed by BoltzYAMLCombinerNode. If affinity properties are included, there must also be at least one ligand sequence.</td><td style="word-wrap: break-word;">{   "version": 1,   "sequences": [     {"protein": {"id": "A", "sequence": "DVVYENRILE...", "msa": "msa_1.a3m"}},     {"ligand": {"id": "L", "smiles": "CC1=CC=CC=C1"}}   ],   "properties": [     {"affinity": {"binder": "L"}}   ] }</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary file map produced alongside the YAML, associating filenames (MSA and template files) to their textual contents. Keys are file names used inside the YAML (for example msa_1.a3m, template_1.pdb) and values are the corresponding file contents as strings.</td><td style="word-wrap: break-word;">{   "msa_1.a3m": ">seqA/1-120\nDVVYENRILE...\n",   "template_1.pdb": "ATOM      1  N   MET A   1      10.234  13.567 ..." }</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed for the Boltz diffusion sampling process. Controls reproducibility across runs: the same seed and identical configuration should yield the same ensemble of structures.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of structure recycling iterations used by the model. Higher values typically improve refinement but increase runtime. Must be between 1 and 20.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps per trajectory. Larger values can give higher-quality structures at the cost of computation. Valid range is 50–500.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of independent diffusion samples to generate per configuration. Each sample produces its own set of ranked structures. Range: 1–25.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to run in parallel on backend resources. Tuning this allows you to balance throughput versus GPU memory and queue pressure. Range: 1–10.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scaling factor (temperature-like parameter). Values closer to 1.0 are more conservative; higher values up to 2.0 explore more of conformational space. Default is 1.638.</td><td style="word-wrap: break-word;">1.5</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Desired coordinate output format for ranked structures: "pdb" or "mmcif". When mmcif is selected, returned structure keys use .cif extensions even though the backend reports .pdb names.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes used within the backend for multiprocessing. 0 disables multiprocessing. Range: 0–8.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Upper bound on the number of sequences in the multiple sequence alignment used per chain. Used to limit memory and runtime. Range: 256–16384.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample the MSA when it exceeds a given size, selecting a subset of sequences for inference. Requires num_subsampled_msa to be set appropriately.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Target number of sequences to retain when MSA subsampling is enabled. Must lie between 256 and 4096 and should be lower than or equal to max_msa_seqs.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Toggle inference-time potentials that can improve structure quality, at the cost of additional compute. Recommended to enable when accuracy is more important than speed.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests the backend to write full pairwise aligned error (PAE) matrices, not just summarized metrics. Results are returned as decoded JSON matrices.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, requests the backend to write full pairwise distance error (PDE) matrices, allowing detailed structural uncertainty analysis.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When affinity prediction is enabled (via properties and ligands), this applies a molecular weight correction to the predicted binding affinity, if supported by the backend.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps dedicated to affinity estimation. Only used when affinity prediction is requested. Range: 50–500.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples used specifically for affinity prediction. Higher values may stabilize affinity estimates but increase runtime. Range: 1–10.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping structure filenames to coordinate file contents for all ranked predictions. Keys are filenames such as rank_001_model_1_seed42.pdb (or .cif when output_format is mmcif), and values are the corresponding PDB or mmCIF text. These are the primary 3D structures for downstream visualization or analysis.</td><td style="word-wrap: break-word;">{   "rank_001_model_1_seed42.pdb": "ATOM      1  N   MET A   1      11.234  15.678 ...\nEND\n",   "rank_002_model_2_seed42.pdb": "ATOM      1  N   MET A   1       9.845  16.102 ...\nEND\n" }</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Per-model confidence summaries returned by the backend, keyed by structure identifier. Typically includes global metrics such as mean pLDDT, predicted TM-score, and other model-specific confidence signals.</td><td style="word-wrap: break-word;">{   "rank_001_model_1_seed42": {     "mean_plddt": 89.7,     "ptm": 0.86,     "iptm": 0.83   },   "rank_002_model_2_seed42": {     "mean_plddt": 84.3,     "ptm": 0.81,     "iptm": 0.78   } }</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction outputs when affinity is requested via the YAML (that is, at least one ligand and an affinity property). Keys typically correspond to ligand–target complexes; values contain predicted binding metrics such as delta G or pKD. Empty if no affinity property is configured.</td><td style="word-wrap: break-word;">{   "complex_A_L": {     "predicted_dG_kcal_per_mol": -9.3,     "predicted_pKd": 8.4,     "mw_corrected": true   } }</td></tr>
<tr><td style="word-wrap: break-word;">pae.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Decoded pairwise aligned error (PAE) matrices for each predicted model, derived from base64-encoded NPZ files. Top-level keys are model IDs; nested keys are PAE array names, each containing 2D lists of floats suitable for plotting or further analysis.</td><td style="word-wrap: break-word;">{   "rank_001_model_1_seed42": {     "pae": [[0.5, 1.2, 1.8], [1.1, 0.4, 1.5], [1.9, 1.4, 0.3]]   } }</td></tr>
<tr><td style="word-wrap: break-word;">pde.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Decoded pairwise distance error (PDE) matrices, in the same structure as pae.json. Useful for detailed analysis of distance uncertainties between residue pairs in the predicted structure.</td><td style="word-wrap: break-word;">{   "rank_001_model_1_seed42": {     "pde": [[0.2, 0.8, 1.1], [0.7, 0.3, 0.9], [1.0, 0.8, 0.2]]   } }</td></tr>
<tr><td style="word-wrap: break-word;">plddt.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Decoded per-residue pLDDT values from NPZ into JSON-serializable lists. Keys are model IDs and values contain arrays such as plddt with a list of floats (0–100) for each residue.</td><td style="word-wrap: break-word;">{   "rank_001_model_1_seed42": {     "plddt": [92.1, 90.4, 88.7, 85.3, 81.9]   } }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Runtime is dominated by GPU-side Boltz2 inference; parameters such as sampling_steps, diffusion_samples, diffusion_samples_affinity, and use_potentials substantially increase runtime but may improve quality.
- **Limitations**: The node enforces that Boltz YAML contains at least one sequence and that affinity prediction is only allowed when at least one ligand is present; misconfigured YAML will raise validation errors before dispatch.
- **Behavior**: When output_format is mmcif, returned structure keys are rewritten from .pdb to .cif, even though the backend reports .pdb filenames internally.
- **Behavior**: PAE, PDE, and pLDDT are returned as JSON-serializable structures converted from base64-encoded NPZ files; large systems will result in large JSON objects, which may impact downstream node memory usage.
- **Performance**: max_parallel_samples and num_workers control concurrency on the backend; overly aggressive settings can lead to queuing or resource contention without significantly reducing wall-clock time.
- **Limitations**: If the YAML includes affinity properties but no ligand sequences, the node will fail fast with a clear error instead of running an invalid job.

## Troubleshooting
- **Invalid Boltz YAML**: Error messages such as "Boltz YAML must contain at least one sequence" indicate that boltz_yaml was not produced correctly (for example, bypassed BoltzYAMLCombinerNode or passed an empty list). Reconnect your sequence builders and recombiner to ensure a non-empty sequences array.
- **Affinity without ligand**: Error "Affinity prediction requires at least one ligand sequence" occurs when your YAML defines an affinity property but no ligand entries in sequences. Add a ligand via BoltzLigandSequenceNode and reconnect, or remove the affinity property.
- **Wrong types for inputs**: Errors like "Boltz YAML must be a dictionary" or "Boltz files must be a dictionary" mean that some upstream node produced a list or raw string instead of the expected typed object. Ensure you are wiring outputs from BoltzYAMLCombinerNode directly into boltz_yaml and boltz_files.
- **Large job timeouts**: Very large systems with high sampling_steps, high diffusion_samples, and use_potentials set to true can approach the 10800-second timeout. Reduce sampling parameters, diffusion samples, or disable potentials if you encounter timeouts from the Boltz2 backend.
- **Empty affinity.json**: If affinity.json is returned as an empty object while you expected values, verify that your YAML properties include an affinity block and that at least one ligand sequence is present and correctly referenced as the binder chain.
