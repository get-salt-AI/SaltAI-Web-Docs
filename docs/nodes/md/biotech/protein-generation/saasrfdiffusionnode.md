# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs the RFdiffusion protein design/generation service. Supports unconditional generation (length-only) and conditional workflows including motif scaffolding, partial diffusion, symmetry, denoiser tuning, and guiding potentials. Returns the designed structure and, when enabled, trajectory snapshots.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate new protein backbones or redesign around existing motifs/structures. For unconditional designs, provide only a length range via contigs. For conditional or partial diffusion, provide an input PDB and a contig layout describing fixed motifs and flexible regions. Optional configuration nodes (Symmetry, Contigmap, Denoiser, Potentials) can be connected to fine-tune behavior.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Model checkpoint to use. Auto selects appropriate checkpoint based on inputs; manual selection overrides.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes trajectory PDBs (Xt-1 and pX0) in outputs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Sampler/runner variant to use.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns the model's predicted motif to the input motif in conditional runs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stopping step (1–50). Lower values speed up inference with potential quality trade-offs.</td><td style="word-wrap: break-word;">49</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence layout. Unconditional: length-only (e.g., 100-100). Conditional: combine fixed motifs (A10-25), flexible gaps (5-15), and chain breaks (/0). For partial diffusion, must match the full layout of the input PDB.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated residues on the target for interface design; use ChainIndex format.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Start timestep for denoising (0–50). 50 disables partial diffusion. Values <50 require an input PDB and full contig layout.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed; ensures determinism and is used as design_startnum.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Execution mode. PROD runs the service, TEST uses faster settings, MOCK returns predefined data.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the diffusion process.</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional input structure. Required for conditional scaffolding and partial diffusion.</td><td style="word-wrap: break-word;"><pdb-object-or-upstream-pdb-output></td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Advanced symmetry settings (from RF Diffusion Symmetry Config node).</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Advanced contig mapping settings (from RF Diffusion Contigmap Config node), including inpainting and length constraints.</td><td style="word-wrap: break-word;">{'length': '100-120', 'inpaint_str': 'B165-178'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Denoiser schedule/scales (from RF Diffusion Denoiser Config node).</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Guiding potentials configuration (from RF Diffusion Potentials Config node).</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Designed protein structure(s).</td><td style="word-wrap: break-word;"><pdb-file-for-design_0></td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot at Xt-1 (if write_trajectory is true).</td><td style="word-wrap: break-word;"><pdb-file-for-trajectory_Xt-1_0></td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot for predicted X0 (if write_trajectory is true).</td><td style="word-wrap: break-word;"><pdb-file-for-trajectory_pX0_0></td></tr>
</tbody>
</table>
</div>

## Important Notes
- For unconditional generation: do not pass input_pdb; contigs must be numeric length only (e.g., 100-100); hotspot_res must be empty; in contigmap_config, only length may be set.
- For conditional or motif scaffolding: provide input_pdb and set contigs to include at least one fixed region (e.g., A10-25) unless running partial diffusion.
- Partial diffusion: set partial_T to a value <50 and provide input_pdb. The contigs layout must fully match the residue counts and chain order of the input structure.
- Setting partial_T to 50 disables partial diffusion and is treated as None.
- Trajectory outputs are only produced when write_trajectory is true. TEST mode forces write_trajectory off for speed.
- Auto checkpoint is recommended. Manually selected checkpoints must be compatible with your task (e.g., inpainting, complexes).
- Seed is used deterministically; multiple output files may have seed offsets embedded in metadata.
- Timeout should cover model runtime; increase for long or complex designs.

## Troubleshooting
- Validation error: Non-empty contigs is required. Provide a length-only range for unconditional or a proper layout for conditional/partial diffusion.
- Validation error (unconditional): contigs contains chain letters. Use numeric length-only format like 100-100.
- Validation error (unconditional): hotspot_res provided. Remove hotspot_res for unconditional runs.
- Validation error (unconditional): contigmap_config contains fields other than length. Clear inpaint/sequence/structure fields for unconditional runs.
- Validation error (partial diffusion): input_pdb not provided. Provide input_pdb when partial_T < 50.
- Validation error (conditional): contigs missing any fixed regions. Include at least one fixed segment (e.g., A10-25) unless doing partial diffusion.
- No trajectories returned: write_trajectory may be false or TEST mode is enabled.
- Run timed out: increase the timeout input, reduce final_step, or switch to TEST mode to verify pipeline and speed.
