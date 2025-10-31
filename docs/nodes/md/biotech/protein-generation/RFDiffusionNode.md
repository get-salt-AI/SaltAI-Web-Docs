# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein backbone designs using an RF Diffusion sampler. Supports unconditional generation from a length range or conditional tasks such as motif scaffolding, partial diffusion, symmetry, denoiser tuning, and guiding potentials. Offers deterministic runs via a seed and optional trajectory outputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/RFDiffusionNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design protein structures either from scratch (unconditional) or conditioned on an input PDB and contig layout (conditional). For unconditional designs, provide a numeric length range in contigs and leave input_pdb empty. For conditional or partial diffusion tasks, provide input_pdb and a contig layout that specifies fixed regions; you can also add optional configs for symmetry, contig mapping (mask/provide regions, length), denoiser behavior, and guiding potentials.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Checkpoint selection. Auto will choose the most appropriate checkpoint based on inputs; selecting a specific checkpoint requires compatibility with your task.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include trajectory outputs from the diffusion process.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sampler variant to use.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns the model’s predicted motif to the input motif during conditional tasks.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stopping step (1–50). Larger values reduce compute; stopping early speeds up inference.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence layout. Unconditional: specify only a numeric length range (e.g., 100-100). Conditional: combine fixed motif ranges (e.g., A10-25), flexible gaps (5-15), and chain breaks (/0). For partial diffusion (partial_T != 50), specify the full layout matching the input PDB residue counts and order.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residues on the target protein that should contact the binder. Comma-separated chain-index pairs. Leave empty for unconditional generation.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Starting timestep for denoising (0–50). Set to 50 to disable (full diffusion). Values below 50 enable partial diffusion from a partially noised structure.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic runs.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. PROD runs the service, MOCK returns predefined data, TEST uses minimal parameters for faster runs.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Input structure for conditional tasks (motif scaffolding or partial diffusion). Leave empty for unconditional generation.</td><td style="word-wrap: break-word;">PDB handle from an upstream node</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Additional symmetry configuration. Build via RF Diffusion Symmetry Config node.</td><td style="word-wrap: break-word;">{'symmetry': 'c2', 'recenter': True, 'radius': 10, 'model_only_neighbors': False, 'symmetric_self_cond': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Additional contig mapping configuration (masking structure/sequence, provided regions, length). Build via RF Diffusion Contigmap Config node.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A50-80', 'inpaint_str': 'B165-178', 'provide_seq': '172-177,200-205', 'length': '100-150'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Denoiser noise scales and schedules for translations and rotations. Build via RF Diffusion Denoiser Config node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0, 'ca_noise_schedule_type': 'constant', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 1.0, 'frame_noise_schedule_type': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Guiding potentials configuration (e.g., contacts, ROG, substrate interactions). Build via RF Diffusion Potentials Config node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'LLK'}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">The designed protein backbone(s).</td><td style="word-wrap: break-word;">PDB handle containing generated structure(s)</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional trajectory at Xt-1 steps if write_trajectory is true.</td><td style="word-wrap: break-word;">PDB handle for Xt-1 trajectory frames</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional posterior mean trajectory (pX0) if write_trajectory is true.</td><td style="word-wrap: break-word;">PDB handle for pX0 trajectory frames</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unconditional vs Conditional**: Unconditional requires no input_pdb and contigs must be numeric length range only (e.g., 100-100). Conditional requires input_pdb and a contig layout containing at least one fixed region (e.g., A10-25), unless you are doing partial diffusion.
- **Partial diffusion**: Set partial_T to 50 to disable. Values below 50 enable partial diffusion and require input_pdb. For partial diffusion, ensure contigs fully reflect the exact residue counts and order in the input PDB.
- **Hotspot residues**: Must be empty for unconditional generation.
- **Contigmap config in unconditional mode**: Only length is applicable; other fields should be empty.
- **Determinism**: seed is used to produce deterministic outputs; the node derives per-sample seeds internally.
- **Trajectory outputs**: trajectory_Xt-1.pdb and trajectory_pX0.pdb are only produced when write_trajectory is true.
- **Mode behavior**: TEST mode reduces compute by adjusting parameters (e.g., increases final_step number for speed and disables trajectory and cautious behavior). MOCK mode returns predefined data for quick prototyping.

## Troubleshooting
- **Error: Non empty contigs must be passed**: Provide a valid contigs string. For unconditional use a numeric range (e.g., 120-120). For conditional include fixed regions and optional gaps/breaks (e.g., 5-15/A10-25/30-40/0 B1-100).
- **Error: For partial diffusion sampling, expected input_pdb to be provided**: When partial_T < 50, you must supply an input_pdb.
- **Error: For unconditional sampling, expected contigs numeric**: If contigs includes chain letters (e.g., A10-25) while input_pdb is empty, switch to conditional mode or use a numeric length range only.
- **Error: For unconditional sampling, hotspot_res must be empty**: Remove hotspot_res entries for unconditional runs.
- **Error: Unexpected contigmap_config fields in unconditional mode**: Only length is allowed; clear inpaint/provide fields.
- **Conditional contigs missing fixed region**: If input_pdb is provided and partial_T is disabled (50), include at least one fixed region (e.g., A10-25) in contigs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-generation/RFDiffusionNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

