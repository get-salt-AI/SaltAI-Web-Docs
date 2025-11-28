# Boltz Partial Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Boltz partial diffusion using a reference structure to refine or sample structures while optionally fixing specific chains. Supports both single-chain and multimer systems, can save diffusion trajectories, and can include affinity prediction when requested in the YAML properties.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpartialdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building Boltz YAML and auxiliary files. Provide a reference PDB structure to guide partial diffusion. Optionally fix selected chains, set the diffusion fraction, and enable trajectory saving for analysis. Integrate it in workflows where you want to refine models toward a reference, sample alternative conformations, or predict affinity (when a ligand and affinity property are present).

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz YAML configuration that defines sequences (and optionally constraints, templates, and properties).</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'ACDEFGHIKLM'}}], 'properties': [{'affinity': {'binder': 'L'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the YAML (e.g., MSA .a3m files, template PDB files).</td><td style="word-wrap: break-word;">{'msa_1.a3m': '<a3m-contents>', 'template_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">reference_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Reference structures in PDB format that guide the partial diffusion process. Provide as a mapping of filename to PDB text.</td><td style="word-wrap: break-word;">{'reference.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed; each sequence may be offset internally to produce multiple samples deterministically.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">partial_diffusion_fraction</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Fraction of the diffusion process to perform (0.0 = no diffusion, 1.0 = full diffusion). Lower values keep models closer to the reference.</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">save_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, saves intermediate diffusion trajectory frames for analysis.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">fixed_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to keep fixed (e.g., A,B). Chains must exist in the YAML sequences.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling steps during prediction.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps for diffusion.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples to generate per sequence.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of diffusion samples to run in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Output format for structures. Allowed values: pdb, mmcif.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes to use (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use inference-time potentials. Recommended for partial diffusion.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes full PAE matrices to the confidence outputs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes full PDE matrices to the confidence outputs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, subsamples the MSA to reduce size.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sequences to keep when subsampling the MSA.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction for affinity prediction (only relevant if affinity property is present).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps used specifically for affinity prediction.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples used for affinity prediction.</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ranked predicted structures produced by partial diffusion. Keys may be prefixed with sequence names when multiple sequences are present.</td><td style="word-wrap: break-word;">{'sequence_0_rank_1_model_1.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics (e.g., pLDDT/PAE) for the generated structures, optionally including full matrices if enabled.</td><td style="word-wrap: break-word;">{'sequence_0_confidence.json': '{"pLDDT": [...], "PAE": [...]}'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory frames from the diffusion process if trajectory saving is enabled. May be empty if not requested.</td><td style="word-wrap: break-word;">{'sequence_0_traj_000.pdb': '<pdb-contents>'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction outputs when the YAML includes an affinity property and a binder. Empty if affinity is not requested.</td><td style="word-wrap: break-word;">{'sequence_0_affinity.json': '{"Kd": 1.2, "units": "uM"}'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Reference structures are required**: Provide PDB-format reference structures as a mapping name->content; the node will not run without them.
- **Fixed chains must exist**: The fixed_chains list must be a subset of chain IDs declared in the YAML; otherwise validation fails.
- **Affinity output requires affinity property**: The affinity.json output is only populated if the YAML properties include an affinity entry (and the model supports it).
- **Sequence name prefixes**: For multi-sequence inputs, output filenames/keys may be prefixed with sequence names to distinguish results.
- **Trajectory output**: trajectory.pdb will only contain data when save_trajectory is true; otherwise it may be empty.
- **Multimer support**: The node automatically handles single-chain and multimer systems; parameters like fixed_chains are especially useful in multimer scenarios.

## Troubleshooting
- **Error: 'Boltz YAML must contain at least one sequence'**: Ensure boltz_yaml includes a non-empty sequences list.
- **Error: 'Reference structures are required'**: Provide a PDB mapping under reference_structures, e.g., {"reference.pdb": "<pdb-text>"}.
- **Error: 'Fixed chains {…} not found in YAML sequences'**: Check that fixed_chains only includes chain IDs defined under each entity's id in the YAML.
- **Empty trajectory output**: Enable save_trajectory to capture diffusion trajectories.
- **No affinity outputs**: Include an affinity property in boltz_yaml (and ensure a binder chain/ligand is defined) to produce affinity.json.
- **Unexpected empty PDB outputs**: Verify sampling_steps, partial_diffusion_fraction, and that reference_structures content is valid PDB text.
