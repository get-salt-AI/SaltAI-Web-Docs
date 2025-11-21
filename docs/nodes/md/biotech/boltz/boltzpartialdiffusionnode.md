# Boltz Partial Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs partial diffusion-based structure refinement/generation using a Boltz YAML specification and reference structures. Supports single-chain and multimer systems, selectively fixing specified chains and optionally saving diffusion trajectories. Can also compute affinity if requested in properties.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpartialdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling a valid Boltz YAML and auxiliary files (MSAs/templates) with the Boltz YAML Combiner. Provide one or more reference structures in PDB format to anchor partial diffusion. Optionally set which chains remain fixed, the fraction of the diffusion process to run, sampling settings, and whether to save trajectories. If affinity is included in properties, enable the related options to obtain affinity predictions.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz YAML configuration describing sequences (and optionally constraints/templates/properties).</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'MSEQN...', '_sequence_name': 'protA', 'msa': 'msa_1.a3m'}}], 'properties': [{'affinity': {'binder': 'L'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the YAML (e.g., MSA A3M files, template PDB/CIF files) as a mapping from filename to file content.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m-content>', 'template_1.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">reference_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Reference structure(s) in PDB format used to anchor the partial diffusion. Provide as a mapping name -> PDB text.</td><td style="word-wrap: break-word;">{'ref_model.pdb': '<pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed. When multiple sequence entries are present, the seed is incremented per entry.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">partial_diffusion_fraction</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Fraction of the diffusion process to apply (0.0 = no diffusion, 1.0 = full diffusion).</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">save_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, saves the diffusion trajectory (intermediate structures) for analysis.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">fixed_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to keep fixed during partial diffusion. Must match chain IDs defined in YAML.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations during prediction/refinement.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of independent diffusion samples to generate per input.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to run in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter) controlling exploration.</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">['pdb', 'mmcif']</td><td style="word-wrap: break-word;">Output structure format.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of parallel workers (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use inference-time potentials to guide sampling (recommended for partial diffusion).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full Predicted Aligned Error matrix to confidence output.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full Predicted Distance Error matrix to confidence output.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to keep when subsampling is enabled.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction to affinity predictions (effective when affinity property is requested).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps dedicated to affinity prediction (when requested).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples for affinity prediction (when requested).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted/refined structure files as a mapping from name to PDB text. May include multiple ranked structures per input.</td><td style="word-wrap: break-word;">{'sequence_0_ranked_0.pdb': '<pdb-text>', 'sequence_0_ranked_1.pdb': '<pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics per output (e.g., pLDDT/PAE/PDE), keyed by name.</td><td style="word-wrap: break-word;">{'sequence_0_confidence.json': '{"plddt": [...], "pae": [...]}'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Saved diffusion trajectory PDB(s) when trajectory saving is enabled; otherwise may be empty.</td><td style="word-wrap: break-word;">{'sequence_0_trajectory.pdb': '<multi-model-pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction outputs when affinity is requested in properties; empty otherwise.</td><td style="word-wrap: break-word;">{'sequence_0_affinity.json': '{"KD": 1.2, "units": "uM"}'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Reference structures required**: You must provide at least one PDB reference structure; this anchors partial diffusion.
- **Chain IDs must match**: Any fixed_chains must correspond to chain IDs defined in the YAML sequences.
- **Trajectory output**: trajectory.pdb is only populated if save_trajectory is true.
- **Affinity outputs**: affinity.json is produced only when the YAML includes an affinity property and the system is set up for affinity prediction; including a ligand sequence is typically required.
- **Multimer handling**: The node automatically detects multimer vs. single-chain mode based on the number of unique chain IDs in the YAML.
- **Per-sequence batching**: If multiple sequence entries exist in the YAML, the node processes them sequentially and aggregates outputs, incrementing the seed for each.
- **MSA/templates linkage**: Ensure boltz_files contains all filenames referenced in boltz_yaml (e.g., msa_*.a3m, template_*.pdb).
- **Partial diffusion fraction**: Lower values keep the structure closer to the reference; higher values allow more deviation.

## Troubleshooting
- **Error: 'Fixed chains {...} not found in YAML sequences'**: Check fixed_chains values and ensure all chain IDs are defined in the YAML sequences.
- **Empty trajectory output**: Set save_trajectory to true to record intermediate diffusion states.
- **Missing affinity output**: Verify that boltz_yaml includes an affinity property and that the system includes a ligand sequence; enable affinity-related options if needed.
- **Invalid YAML or files**: Ensure boltz_yaml is a dictionary with at least one sequence and boltz_files is a dictionary mapping filenames to contents; all referenced files must exist in boltz_files.
- **PDB parsing issues**: Confirm reference_structures is a dict with at least one valid PDB text value.
- **Unexpected few outputs for multimers**: Verify that all chains and MSAs are correctly specified; check that fixed_chains does not unintentionally constrain all chains.
- **Performance or timeout concerns**: Reduce diffusion_samples, sampling_steps, or max_parallel_samples; consider lowering save_trajectory or enabling multiprocessing via num_workers.
