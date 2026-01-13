# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein structures using RFdiffusion with optional conditional controls, symmetry, denoising schedules, guiding potentials, and scaffold-guided (fold-conditioned) settings. Supports both unconditional sequence-length based generation and conditional design from an input PDB, including partial diffusion and protein–protein interaction guidance. Deterministic by default using the provided seed, with optional trajectory outputs for analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design or sample protein backbones. For unconditional sampling, provide only a length range in contigs. For conditional design, supply an input PDB and specify contigs with fixed regions and flexible gaps; optionally enable scaffold-guided fold conditioning and PPI design via their respective config nodes. Connect optional configuration nodes to fine-tune symmetry, contig masking, denoising, and potentials. Choose PROD for real runs, TEST for quick checks, or MOCK to return precomputed demo results.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">ENUM[Auto, Base, Complex_base, Complex_Fold_base, InpaintSeq, InpaintSeq_Fold, ActiveSite, Base_epoch8, Complex_beta]</td><td style="word-wrap: break-word;">Model checkpoint selection. Auto chooses the best checkpoint based on inputs; manual selection requires compatibility with your setup.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also outputs intermediate trajectory PDBs.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">ENUM[default, SelfConditioning, ScaffoldedSampler]</td><td style="word-wrap: break-word;">Sampler variant to run for the diffusion process.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns predicted motif to the input motif when applicable.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stopping step (1–50). Larger values speed up inference by stopping earlier.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines sequence layout. Unconditional: length range only (e.g., 100-100). Conditional: combine fixed motifs (A10-25), flexible gaps (5-15), and chain breaks (/0). For partial diffusion (partial_T != 50), must fully match the input PDB’s residue count/order.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residues on the target protein to encourage interactions (PPI). Comma-separated chain+index (e.g., A30,A33). Leave empty unless a target protein is configured.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Start timestep for denoising (0–50). Set 50 to disable (full diffusion). Values <50 enable partial diffusion and require an input PDB.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic generation.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">ENUM[MOCK, PROD, TEST]</td><td style="word-wrap: break-word;">Run mode. MOCK returns precomputed results, PROD runs the service, TEST uses minimal parameters for quick checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the service call (30–12000).</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input structure for conditional or partial diffusion. Required for partial_T < 50.</td><td style="word-wrap: break-word;">{'file_0.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Symmetry parameters. Connect from RF Diffusion Symmetry Config.</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True, 'radius': 10}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Contig masking, inpainting, and length options. Connect from RF Diffusion Contigmap Config.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A10-20', 'length': '100-150'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">Noise scaling and schedule settings. Connect from RF Diffusion Denoiser Config.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0, 'ca_noise_schedule_type': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Guiding potentials to steer generation. Connect from RF Diffusion Potentials Config.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10, 'guide_decay': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Fold-conditioning and scaffold-guided design parameters; supports PPI with target proteins. Connect from RF Diffusion Scaffold Guided Config.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'scaffold_secstruc_adj': {'type': 'secstruc_adj_input', 'pdbs': {'file_0.pdb': '<pdb-content>'}}}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Final generated protein structure(s).</td><td style="word-wrap: break-word;">{'design_0.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Intermediate trajectory at Xt-1, if write_trajectory is true.</td><td style="word-wrap: break-word;">{'design_0_Xt-1.pdb': '<pdb-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted pX0 trajectory, if write_trajectory is true.</td><td style="word-wrap: break-word;">{'design_0_pX0.pdb': '<pdb-content>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Partial diffusion: Set partial_T to 50 to disable. Any value <50 requires an input PDB and a full contig layout matching the PDB’s residue order.
- Unconditional vs conditional: Unconditional requires contigs with length-only ranges (e.g., 100-100) and hotspot_res must be empty. Conditional requires at least one fixed region (e.g., A10-25) unless scaffold-guided mode is enabled.
- Scaffold-guided mode: contigs may be omitted because they are auto-generated from the scaffold. If specifying hotspot_res in scaffold-guided mode, you must also provide a target protein via Scaffold Guided Config.
- Determinism: The seed controls design numbering and enables deterministic outputs for the same configuration.
- Trajectory outputs: trajectory_Xt-1.pdb and trajectory_pX0.pdb are only returned when write_trajectory is true.
- Mode behavior: TEST mode reduces final_step and disables cautious mode and trajectories for speed; MOCK returns precomputed demo results; PROD performs real generation.
- Checkpoint Auto: Auto selects an appropriate checkpoint based on inputs; manual checkpoints may not support all configurations.
- Timeout: Ensure timeout is sufficient for complex runs; large systems or trajectories may require higher values.

## Troubleshooting
- Error: Non empty `contigs` must be passed: Ensure contigs is provided unless using scaffold-guided mode, where contigs can be omitted.
- Error: For partial diffusion sampling, expected `input_pdb` to be provided: Supply an input PDB when partial_T < 50.
- Error: For unconditional sampling, expected `contigs` to specify only length range: Use formats like 100-100 and avoid chain letters in unconditional mode.
- Error: For unconditional sampling, expected `hotspot_res` to be empty: Remove hotspot_res for unconditional runs.
- Error: For unconditional sampling, expected `<param>` to be empty but got ...: In contigmap_config only `length` may be set; clear other contigmap fields in unconditional mode.
- Error: For conditional sampling, expected contigs to specify least one fixed region: Include at least one region with chain letters (e.g., A10-25) unless in scaffold-guided mode.
- Error: hotspot_res specified but no target protein configured: When using hotspot_res in scaffold-guided design, also provide target_pdb (and ideally target_pdbs) in the Scaffold Guided Config.
- Unexpected early stop or no trajectories in TEST: TEST mode forces final_step=49 and disables trajectory writing for speed; switch to PROD and enable write_trajectory.
