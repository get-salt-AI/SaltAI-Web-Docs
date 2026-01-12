# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein structures using the RFdiffusion algorithm. Supports unconditional generation from length ranges, conditional generation with motifs/contigs, and scaffold-guided/fold-conditioned design. Can optionally return denoising trajectories and integrates with symmetry, contigmap, denoiser, potentials, and scaffold-guided configuration nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design novel proteins or binders. For unconditional generation, provide only a length range via contigs. For conditional or scaffold-guided design, connect an input PDB and optional configuration nodes (symmetry/contigmap/denoiser/potentials/scaffold-guided). Adjust write_trajectory, model runner, and diffusion steps to balance speed and detail; use TEST mode for quick iteration and PROD for full runs.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Model checkpoint selection. 'Auto' chooses based on inputs; others pick specific RFdiffusion checkpoints.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also produces trajectory PDBs from the diffusion process.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Sampler variant for the model.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns predicted motif to the input motif when present.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stopping step (1-50). Larger values speed up inference.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines sequence layout. Unconditional: only a length range (e.g., 100-100). Conditional: combine fixed ranges and gaps, with optional chain breaks. For partial diffusion, must exactly match the input PDB residue layout.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target residues involved in interactions (for PPI design). Format: ChainID+index list.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Start timestep for denoising (0-50). 50 disables partial diffusion.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic generation.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Execution mode. MOCK uses predefined outputs, TEST reduces compute for quick checks, PROD performs full runs.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout for the RFdiffusion process in seconds.</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Starting structure for conditional design or partial diffusion.</td><td style="word-wrap: break-word;">{'filename': 'input.pdb', 'content': '<pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Symmetry settings. Connect from RF Diffusion Symmetry Config.</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Contig/inpainting/length settings. Connect from RF Diffusion Contigmap Config.</td><td style="word-wrap: break-word;">{'length': '120-150', 'provide_seq': '172-177'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">Noise schedule parameters. Connect from RF Diffusion Denoiser Config.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Guiding potentials to steer design (e.g., contacts, ROG). Connect from RF Diffusion Potentials Config.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10}</td></tr>
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Fold conditioning / scaffold-guided settings and optional target protein for PPI. Connect from RF Diffusion Scaffold Guided Config.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'scaffold_secstruc_adj': {'type': 'secstruc_adj_input'}}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Final generated protein structure.</td><td style="word-wrap: break-word;">{'filename': 'generation_0.pdb', 'content': '<pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Intermediate Xt-1 trajectory structure when write_trajectory is true.</td><td style="word-wrap: break-word;">{'filename': 'trajectory_Xt-1_0.pdb', 'content': '<pdb-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted X0 trajectory structure when write_trajectory is true.</td><td style="word-wrap: break-word;">{'filename': 'trajectory_pX0_0.pdb', 'content': '<pdb-text>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For unconditional sampling: contigs must be only a length range (e.g., 100-100), hotspot_res must be empty, and contigmap_config fields other than length must be empty.
- For conditional sampling: provide input_pdb and contigs must include at least one fixed region (e.g., A10-25) unless using scaffold-guided mode.
- Partial diffusion requires input_pdb and partial_T < 50; if partial_T = 50, partial diffusion is disabled.
- In scaffold-guided mode, contigs may be omitted since they can be auto-generated from the scaffold structure.
- Specifying hotspot_res in scaffold-guided mode requires a configured target protein; otherwise, leave hotspot_res empty.
- TEST mode reduces runtime by setting final_step to 49 and disabling trajectory writing and cautious mode; use PROD for full-quality results.
- Trajectory outputs are only populated when write_trajectory is true.
- Set a sufficient timeout for large or complex designs; increases in final_step or added potentials can require more time.

## Troubleshooting
- Error: Non empty `contigs` must be passed – Ensure contigs is provided unless scaffold-guided mode is enabled.
- Error: For partial diffusion sampling, expected `input_pdb` to be provided – Connect an input PDB and set partial_T < 50, or set partial_T to 50 to disable.
- Error: For unconditional sampling, expected `contigs` to specify only length range – Use a format like 120-120 and clear chain/region annotations.
- Error: For unconditional sampling, expected `hotspot_res` to be empty – Remove hotspot_res or switch to a conditional/scaffold-guided setup.
- Error: For conditional sampling, expected contigs to specify at least one fixed region – Include entries like A10-25 or enable scaffold-guided mode.
- Error: hotspot_res specified but no target protein – Add target_pdb and target_pdbs in the Scaffold Guided Config, or clear hotspot_res.
- Outputs missing trajectories – Enable write_trajectory to receive trajectory_Xt-1.pdb and trajectory_pX0.pdb.
- Timeout exceeded – Increase the timeout input or simplify the configuration (reduce final_step, disable trajectories, or use TEST mode for iteration).
