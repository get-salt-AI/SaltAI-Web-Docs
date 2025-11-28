# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein backbones using RFdiffusion with support for both unconditional generation and conditional motif scaffolding. Allows fine-grained control via symmetry, contig mapping, denoiser, and guiding potentials configurations. Supports full diffusion or partial diffusion (starting from a specified timestep) and can optionally output diffusion trajectories.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design protein structures. For unconditional generation, provide only a length range in contigs (e.g., 100-100) and leave input_pdb empty. For conditional motif scaffolding or partial diffusion, provide an input PDB and a contigs string that specifies fixed and flexible regions. Optionally connect configuration nodes for symmetry, contig map, denoiser, and potentials to refine behavior. Enable write_trajectory if you need intermediate trajectory outputs.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Model checkpoint to use. 'Auto' chooses an appropriate checkpoint automatically; other options are advanced and should match your task.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include trajectory outputs. Disable for faster runs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sampler to run the model with.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Align the model’s predicted motif to the input motif during conditional runs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion will stop at this step (1–50). Higher values speed up inference.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines sequence layout. Unconditional: length range only (e.g., 100-100). Conditional: fixed motifs (A10-25), flexible gaps (5-15), and optional chain breaks (/0). For partial diffusion, must match the full layout of the input PDB.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated target residues that should contact the designed chain(s). Only for conditional runs.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Start denoising from this timestep (0–50). 50 disables partial diffusion (standard full diffusion). Requires input_pdb when < 50.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for reproducibility.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. PROD runs the service, MOCK returns predefined data, TEST uses faster minimal parameters.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the diffusion process (30–12000).</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input PDB for conditional tasks (motif scaffolding or partial diffusion). Not used for unconditional generation.</td><td style="word-wrap: break-word;"><pdb-file-reference></td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Symmetry settings. Generate via the RF Diffusion Symmetry Config node and connect here.</td><td style="word-wrap: break-word;">{'symmetry': 'c2', 'recenter': True, 'radius': 10, 'model_only_neighbors': False, 'symmetric_self_cond': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Contigmap settings for masking/unmasking sequence or structure. Generate via the RF Diffusion Contigmap Config node.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A50-80', 'inpaint_str': 'B165-178', 'provide_seq': '172-177,200-205', 'length': '100-120'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Denoiser noise schedules. Generate via the RF Diffusion Denoiser Config node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0, 'ca_noise_schedule_type': 'constant', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 1.0, 'frame_noise_schedule_type': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Guiding potentials to steer design (e.g., contacts, ROG, substrate contacts). Generate via the RF Diffusion Potentials Config node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Final designed protein structure(s).</td><td style="word-wrap: break-word;"><pdb-file-reference></td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Denoising trajectory at Xt-1 steps. Returned only if write_trajectory is true.</td><td style="word-wrap: break-word;"><pdb-file-reference></td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Predicted X0 trajectory. Returned only if write_trajectory is true.</td><td style="word-wrap: break-word;"><pdb-file-reference></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Checkpoint 'Auto' is recommended; manual checkpoints must be compatible with your task.
- Unconditional mode: contigs must specify only a length range (e.g., 100-100), input_pdb must be empty, and hotspot_res must be empty.
- Conditional mode (motif scaffolding): provide input_pdb and contigs must include at least one fixed region (e.g., A10-25).
- Partial diffusion requires input_pdb and partial_T < 50; partial_T = 50 disables partial diffusion (full diffusion).
- For unconditional runs, contigmap_config must not include fields other than length; any other populated field will raise an error.
- write_trajectory increases runtime and output size; disable for faster testing.
- TEST mode adjusts parameters for speed (e.g., increases final_step, disables trajectories/cautious behavior).
- Seeds are applied per output; downstream files may carry seed metadata.
- Symmetry, contig map, denoiser, and potentials are optional but powerful for advanced control.

## Troubleshooting
- Error: 'Non empty contigs must be passed' – Ensure contigs is provided. For unconditional, use a simple range; for conditional, include fixed regions.
- Error: 'For partial diffusion sampling, expected input_pdb to be provided' – Provide an input PDB when partial_T < 50.
- Error: 'For unconditional sampling, expected contigs to specify only length range' – Remove chain IDs and fixed regions; use a numeric range only.
- Error: 'For unconditional sampling, expected hotspot_res to be empty' – Clear hotspot_res when no input_pdb is used.
- Error: 'For unconditional sampling, expected <key> to be empty' – In contigmap_config leave all fields empty except length.
- Error: 'For conditional sampling, expected contigs to specify least one fixed region' – Include at least one region like A10-25 when using input_pdb.
- Timeouts: Increase timeout if designs are large or trajectories are enabled.
- Empty or malformed contigs/hotspot formatting: Use correct formats (e.g., A10-25; hotspots like A30,A33).
