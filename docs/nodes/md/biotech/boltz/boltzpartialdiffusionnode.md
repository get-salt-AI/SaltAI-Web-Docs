# Boltz Partial Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs Boltz partial diffusion using provided reference structures to refine or partially resample protein (and optional ligand) configurations. Supports single-chain and multimer complexes, optional fixed chains, and can save intermediate diffusion trajectories. When affinity properties are requested in the YAML and ligands are present, it also produces affinity-related outputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpartialdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to refine structures guided by an initial/reference PDB. Typical workflow: build sequences, constraints, templates, and properties with the Boltz builders, combine them into a Boltz YAML, provide one or more reference PDB structures, and then run partial diffusion. Enable trajectory saving for analysis, and optionally fix specific chains to remain constrained during diffusion.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Combined Boltz YAML configuration describing sequences, templates, constraints, and optional properties (e.g., affinity).</td><td style="word-wrap: break-word;">{'sequences': [{'protein': {'id': 'A', 'sequence': 'ACDEFGHIKLMNPQRSTVWY'}}], 'properties': [{'affinity': {'target': 'ligand_1'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the Boltz YAML (e.g., template structures, constraint files).</td><td style="word-wrap: break-word;">{'template_A.pdb': '<pdb-content>', 'ligand_params.sdf': '<sdf-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">reference_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Reference structure(s) used to anchor partial diffusion. Provide as a dictionary of {name: pdb_content}.</td><td style="word-wrap: break-word;">{'ref_complex_A_B.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed for reproducibility. For multiple sequence entries, per-sequence seeds may be offset internally.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">partial_diffusion_fraction</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Fraction of the diffusion process to apply (0.0 = no diffusion; 1.0 = full diffusion). Lower values keep structures closer to the reference.</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">save_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, saves the diffusion trajectory as PDBs for analysis.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">fixed_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to keep fixed (must match chain IDs defined in the YAML sequences). Leave empty to allow all chains to move.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling steps during inference.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps in diffusion.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples to generate per run.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of diffusion samples to run in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Output structure format.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes to use (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use inference-time potentials. Recommended for partial diffusion.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, outputs the full PAE matrix in confidence results.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, outputs the full PDE matrix in confidence results.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable MSA subsampling.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to use when subsampling is enabled.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction for affinity prediction (only relevant when affinity is requested in the YAML).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps for affinity prediction (only applies if affinity is requested).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples for affinity prediction (only applies if affinity is requested).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Generated or refined structures keyed by name. Each entry is a PDB string.</td><td style="word-wrap: break-word;">{'seqA_rank_1.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics for generated structures (e.g., pLDDT and optionally full PAE/PDE if enabled).</td><td style="word-wrap: break-word;">{'seqA_confidence.json': {'plddt': [0.85, 0.9], 'pae': None, 'pde': None}}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Diffusion trajectory PDBs if trajectory saving is enabled; otherwise may be empty.</td><td style="word-wrap: break-word;">{'seqA_traj_0.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity prediction outputs when affinity is requested in the YAML and ligands are present; empty otherwise.</td><td style="word-wrap: break-word;">{'seqA_affinity.json': {'predicted_affinity': 1.23}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Reference structures are required and must be provided in PDB format as a dictionary of {name: pdb_content}.
- Chain IDs referenced in fixed_chains must match the IDs defined in the YAML sequences.
- Affinity outputs are only produced when the YAML includes an affinity property and at least one ligand is present.
- Trajectory outputs are only populated if save_trajectory is true.
- For multimers, ensure all chain IDs in the YAML and reference PDBs are consistent.
- MSA-related parameters (max_msa_seqs, subsample_msa, num_subsampled_msa) control multiple sequence alignment usage and can affect runtime and quality.

## Troubleshooting
- Error: 'Boltz YAML must contain at least one sequence' — Ensure your boltz_yaml has a non-empty 'sequences' list.
- Error: 'Reference structures are required for partial diffusion and must be in PDB format' — Provide at least one PDB in reference_structures as a dict {name: pdb_content}.
- Error: 'Fixed chains {...} not found in YAML sequences' — Check that fixed_chains match chain IDs defined under sequences in the YAML.
- No affinity.json output — Confirm your YAML includes an affinity property and that a ligand sequence is defined.
- Empty trajectory output — Set save_trajectory to true to record diffusion trajectories.
- Invalid chain IDs or mismatched chain labeling — Align chain IDs between YAML and reference PDB files.
