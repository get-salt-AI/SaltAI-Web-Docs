# Boltz Partial Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Boltz-guided partial diffusion prediction using provided reference structures. Supports single-chain (simple) and multichain (multimer) systems, can optionally fix specific chains, and can emit diffusion trajectories for analysis. If affinity properties are present in the YAML, it also computes affinity-related outputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/BoltzPartialDiffusionNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to refine or remodel structures starting from reference PDBs rather than generating from scratch. Typical workflow: build sequences/properties with Boltz builders, combine them into a Boltz YAML and auxiliary files, then pass those along with one or more reference PDB structures into this node to perform partial diffusion. Adjust the partial_diffusion_fraction to control how much the model deviates from the reference, and optionally fix chains that should remain static.

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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Boltz configuration dictionary produced upstream (e.g., from a YAML combiner). Must include at least one sequence and may include properties (such as affinity).</td><td style="word-wrap: break-word;">{'sequences': [{'protein': {'id': 'A', 'sequence': 'MSSSS...END'}}], 'properties': [{'affinity': {'target': 'ligand1'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td>True</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by the Boltz YAML (e.g., templates, constraints, ligands).</td><td style="word-wrap: break-word;">{'templates': {'template1.pdb': '<PDB content>'}, 'ligands': {'ligand1.sdf': '<SDF content>'}}</td></tr>
<tr><td style="word-wrap: break-word;">reference_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Reference structure(s) in PDB format to guide partial diffusion. Provide a dictionary mapping names to PDB content. Chain IDs should align with those specified in the Boltz YAML sequences.</td><td style="word-wrap: break-word;">{'ref_model_1.pdb': 'HEADER ...\\nATOM ...\\nEND', 'ref_model_2.pdb': 'HEADER ...\\nATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Random seed. If multiple sequences are present, each is offset by index to ensure unique runs.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">partial_diffusion_fraction</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Fraction of the diffusion process to perform relative to the reference (0.0 = no change from reference, 1.0 = full diffusion).</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">save_trajectory</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, saves intermediate diffusion trajectories for analysis.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">fixed_chains</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to keep fixed (e.g., "A,B"). Must match chain IDs in the Boltz YAML sequences. Leave empty to allow all chains to move.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">recycling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of recycling iterations.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of sampling steps during diffusion.</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples to generate per sequence.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">max_parallel_samples</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of samples to process in parallel.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">step_scale</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Diffusion step scale (temperature-like parameter).</td><td style="word-wrap: break-word;">1.638</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>False</td><td style="word-wrap: break-word;">ENUM[pdb, mmcif]</td><td style="word-wrap: break-word;">Format for output structures.</td><td style="word-wrap: break-word;">pdb</td></tr>
<tr><td style="word-wrap: break-word;">num_workers</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of worker processes to use (0 disables multiprocessing).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">use_potentials</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use inference-time potentials. Recommended for partial diffusion.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pae</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full PAE matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">write_full_pde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, writes the full PDE matrix.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">max_msa_seqs</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of MSA sequences to use.</td><td style="word-wrap: break-word;">8192</td></tr>
<tr><td style="word-wrap: break-word;">subsample_msa</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, subsamples the MSA.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">num_subsampled_msa</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of MSA sequences to retain when subsampling.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">affinity_mw_correction</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Apply molecular weight correction when computing affinity (only used if affinity properties are present).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">sampling_steps_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Sampling steps to use for affinity prediction (only used if affinity properties are present).</td><td style="word-wrap: break-word;">200</td></tr>
<tr><td style="word-wrap: break-word;">diffusion_samples_affinity</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of diffusion samples for affinity prediction (only used if affinity properties are present).</td><td style="word-wrap: break-word;">5</td></tr>
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
<tr><td style="word-wrap: break-word;">structures.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted structure files in the chosen format, returned as a dictionary mapping names to file content.</td><td style="word-wrap: break-word;">{'seqA_ranked_0.pdb': 'HEADER ...\\nATOM ...\\nEND', 'seqA_ranked_1.pdb': 'HEADER ...\\nATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">confidence.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Confidence metrics for the predictions (e.g., per-model scores and matrices), keyed by sequence/model.</td><td style="word-wrap: break-word;">{'seqA_plddt.json': {'mean_plddt': 84.1}, 'seqA_pae.json': {'shape': [200, 200]}}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Diffusion trajectory snapshots in PDB format when trajectory saving is enabled. May be empty if save_trajectory is false.</td><td style="word-wrap: break-word;">{'seqA_traj_step_050.pdb': 'ATOM ...', 'seqA_traj_step_200.pdb': 'ATOM ...'}</td></tr>
<tr><td style="word-wrap: break-word;">affinity.json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Affinity-related outputs if affinity properties were requested; otherwise returned as an empty object.</td><td style="word-wrap: break-word;">{'seqA_affinity.json': {'predicted_kd': 120.5, 'units': 'nM'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input requirements**: boltz_yaml must contain at least one sequence, boltz_files must be a dictionary, and reference_structures must be a non-empty dictionary of valid PDB content.
- **Partial diffusion control**: partial_diffusion_fraction regulates deviation from the reference; lower values keep structures closer to the supplied PDB(s).
- **Chain handling**: fixed_chains must match chain IDs defined in the Boltz YAML sequences. Invalid IDs will cause an error.
- **Mode detection**: The node automatically treats the job as simple (single chain) or multimer (multiple chains) based on chain IDs in the YAML.
- **Trajectories**: trajectory outputs are only populated if save_trajectory is true; enabling this may increase runtime and memory usage.
- **Affinity outputs**: Affinity parameters are only applied when affinity properties are present in the boltz_yaml; otherwise affinity inputs are ignored and the affinity output will be empty.
- **Performance**: num_workers controls multiprocessing; increasing it may speed up processing but requires more system resources.
- **MSA controls**: If subsample_msa is true, only num_subsampled_msa sequences will be used from the MSA up to max_msa_seqs.

## Troubleshooting
- **Error: 'Reference structures are required'**: Ensure reference_structures is a non-empty dictionary and contains valid PDB text.
- **Error: 'Fixed chains ... not found'**: Verify fixed_chains matches chain IDs in the YAML sequences (e.g., A,B). Remove spaces or typos and ensure YAML defines those chain IDs.
- **No affinity output produced**: Confirm boltz_yaml includes an affinity property. Without it, affinity-related settings are ignored and the affinity output will be empty.
- **Empty trajectory output**: Set save_trajectory to true to generate trajectory snapshots.
- **Outputs missing expected models**: Increase diffusion_samples or sampling_steps, and check that partial_diffusion_fraction and step_scale are appropriate for the desired degree of remodeling.
- **MSA too large or slow**: Lower max_msa_seqs or enable subsample_msa and adjust num_subsampled_msa to reduce compute.
