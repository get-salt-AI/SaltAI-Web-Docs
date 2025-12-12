# Boltz Partial Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs partial diffusion structure refinement/generation for Boltz YAML-defined systems using provided reference structures. Supports both single-chain and multimer assemblies, optionally saving diffusion trajectories and estimating affinity when requested in YAML properties.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpartialdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to refine or partially resample structures starting from known/reference PDBs. Provide a Boltz YAML and auxiliary files (typically composed with Boltz YAML Combiner), a dictionary of reference PDB structures, and select diffusion and sampling parameters. Enable trajectory saving for analysis and set fixed chains to constrain parts of the complex. If the YAML includes an affinity property, the node will also compute affinity metrics.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz YAML configuration describing sequences, chains/IDs, constraints, templates, and optional properties (e.g., affinity).</td><td style="word-wrap: break-word;">{'sequences': [{'protein': {'id': 'A', 'sequence': 'ACDEFG...'}}], 'properties': ['affinity']}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files dictionary referenced by the Boltz YAML (e.g., templates, constraints, ligands).</td><td style="word-wrap: break-word;">{'template_1.pdb': '<bytes>', 'ligand_1.sdf': '<bytes>'}</td></tr>
<tr><td style="word-wrap: break-word;">reference_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of reference structures in PDB format used as starting points for partial diffusion.</td><td style="word-wrap: break-word;">{'ref_model_1.pdb': 'ATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for stochastic sampling. Internally offset per sequence entry.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">partial_diffusion_fraction</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Fraction of the diffusion process to run relative to a full diffusion (0.0=no diffusion, 1.0=full diffusion).</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">save_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, saves intermediate diffusion trajectory structures.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">fixed_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to keep fixed during partial diffusion (must match chain IDs defined in YAML). Leave empty to allow all chains to move.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling/refinement iterations.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion sampling steps per sample.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many diffusion samples to generate.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to run in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">["pdb", "mmcif"]</td><td style="word-wrap: break-word;">Output structure format.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use inference-time potentials (recommended for partial diffusion).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, outputs the full Predicted Aligned Error matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, outputs the full Predicted Distance Error matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to subsample MSA sequences.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences after subsampling (used if subsample_msa is true).</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction for affinity (only relevant when affinity is requested in YAML).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps for affinity estimation.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples to use for affinity estimation.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked refined/generated structures from partial diffusion, keyed by sequence/sample identifiers.</td><td style="word-wrap: break-word;">{'seq1_rank1.pdb': 'ATOM ...\nEND\n'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics (e.g., pLDDT/PAE metadata), keyed by sequence/sample identifiers.</td><td style="word-wrap: break-word;">{'seq1_rank1_confidence.json': {'mean_plddt': 83.2}}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional diffusion trajectories as multi-model PDBs if trajectory saving is enabled.</td><td style="word-wrap: break-word;">{'seq1_traj_sample1.pdb': 'MODEL 1\n...\nMODEL 2\n...\nENDMDL\n'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction results when the YAML includes an affinity property; empty otherwise.</td><td style="word-wrap: break-word;">{'seq1_affinity.json': {'Kd_pred': 1.2e-07, 'units': 'M'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **YAML and files must be dictionaries**: boltz_yaml must include a non-empty 'sequences' list; boltz_files must be a dict of auxiliary assets.
- **Reference structures are required**: Provide at least one valid PDB in reference_structures for partial diffusion to run.
- **Chain IDs must match YAML**: Any IDs specified in fixed_chains must exist in the YAML sequence 'id' fields.
- **Multimer mode auto-detected**: If multiple chain IDs are present, the node uses multimer timeouts and processing.
- **Affinity outputs only when requested**: affinity.json is populated only if boltz_yaml properties include an affinity request.
- **Seed handling**: The seed is internally offset per sequence entry to produce distinct samples across sequences.
- **Trajectory output**: Trajectories are only returned if save_trajectory is true.
- **Recommended setting**: use_potentials is enabled by default for better quality in partial diffusion.

## Troubleshooting
- **Validation error: 'Boltz YAML must contain at least one sequence'**: Ensure boltz_yaml is a dict with a non-empty 'sequences' list.
- **Validation error: 'Boltz files must be a dictionary'**: Provide boltz_files as a dictionary of required auxiliary files.
- **Validation error: 'Reference structures are required'**: Pass a dict mapping filenames to PDB contents in reference_structures.
- **Invalid fixed_chains**: If you see an error about unknown chain IDs, adjust fixed_chains to only include chains defined in the YAML.
- **Empty affinity.json**: This is expected unless the YAML includes an affinity property; ensure ligands/properties are correctly set up.
- **No trajectory output**: Enable save_trajectory to receive trajectory.pdb outputs.
- **Timeouts or long runtimes**: Multimers use a longer timeout than single chains; reduce sampling_steps/diffusion_samples or parallelism if needed.
