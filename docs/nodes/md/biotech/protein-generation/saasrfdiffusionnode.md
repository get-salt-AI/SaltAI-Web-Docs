# RF Diffusion

Generates protein backbone structures using RF Diffusion. Supports unconditional sampling from length ranges and conditional motif scaffolding using input PDBs and contigs. Optional symmetry, denoiser, contigmap, and guiding potentials configurations provide fine-grained control over sampling behavior.

## Usage

Use this node to design new protein backbones or scaffold motifs. For unconditional generation, provide only a length range in contigs (e.g., 100-100) and leave input_pdb empty. For motif scaffolding or partial diffusion, supply an input PDB and contigs describing fixed motifs and flexible regions. Optionally connect configuration nodes for symmetry, contigmap, denoising, and potentials to guide the design.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Model checkpoint selection. Auto chooses a suitable checkpoint based on inputs; manual options require compatibility with the task.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, outputs diffusion trajectories alongside the final design.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sampler to use during inference.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns predicted motif to the input motif for conditional tasks.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stopping step (1–50). Higher values accelerate inference.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence layout. Unconditional: only a length/range (e.g., 100-100). Conditional: combine fixed motifs (A10-25), flexible ranges (5-15), and chain breaks (/0). For partial diffusion, it must match the exact residue layout of the input.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated residues on the target protein used for binding/interface design. Chain letter + residue index.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Start timestep for denoising (0–50). Use 50 to disable partial diffusion. Requires an input PDB if set below 50.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed used for deterministic sampling.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution mode. MOCK returns precomputed results, PROD runs full service, TEST reduces steps for faster checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the RF Diffusion run (30–12000).</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional starting structure for conditional generation or partial diffusion.</td><td style="word-wrap: break-word;">{'file_0': '<pdb-file-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Symmetry sampling configuration. Connect output from RF Diffusion Symmetry Config node.</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True, 'radius': 10}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Contig mapping configuration (masking/unmasking sequence/structure, length). Connect output from RF Diffusion Contigmap Config node.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A50-60', 'length': '100-120'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Noise schedule and scale parameters. Connect output from RF Diffusion Denoiser Config node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0, 'ca_noise_schedule_type': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Guiding potentials configuration to bias designs. Connect output from RF Diffusion Potentials Config node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">The generated protein backbone design(s).</td><td style="word-wrap: break-word;">{'file_0': '<pdb-file-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshots at Xt−1 (returned only if write_trajectory is true).</td><td style="word-wrap: break-word;">{'file_0': '<pdb-file-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshots at pX0 (returned only if write_trajectory is true).</td><td style="word-wrap: break-word;">{'file_0': '<pdb-file-reference>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unconditional sampling**: Leave input_pdb empty. contigs must specify only length ranges (e.g., 100-100). hotspot_res must be empty. contigmap_config keys other than length must be empty.
- **Conditional sampling (motif scaffolding)**: Provide input_pdb and contigs that include at least one fixed region with chain letters (e.g., A10-25). For partial diffusion (partial_T < 50), contigs must fully match the residue layout (counts and chain breaks) of the input structure.
- **Partial diffusion**: Setting partial_T to 50 disables partial diffusion; values 0–49 enable it and require input_pdb.
- **Trajectories**: If write_trajectory is false, trajectory outputs will be empty/not produced.
- **TEST mode**: Runs a faster configuration by reducing steps and disabling some overhead for quick validation. Use PROD for full-quality results.
- **Timeouts**: Long or complex designs may require increasing timeout, especially with trajectories enabled or larger complexes.

## Troubleshooting
- **Error: Non empty contigs must be passed**: Provide a valid contigs string. For unconditional, use only a length or length range (e.g., 120-150). For conditional, include fixed motif segments like A10-25 and flexible gaps like 5-15.
- **Unconditional run rejects partial_T < 50**: Partial diffusion requires an input PDB. Either set partial_T to 50 or provide input_pdb.
- **Unconditional run rejects hotspot_res or contigmap settings**: Clear hotspot_res and ensure contigmap_config contains only length. Other contigmap fields (inpaint_seq, inpaint_str, etc.) must be empty.
- **Conditional run complains about missing fixed regions**: Ensure contigs includes at least one fixed region with chain letters (e.g., A10-25) unless performing partial diffusion with a fully matching layout.
- **Partial diffusion with conditional run fails**: Verify contigs exactly match the residue counts, chain order, and breaks of the input PDB (e.g., use 79-79 for a 79-residue monomer; use X- X /0 Y- Y for multi-chain).
- **No trajectory outputs**: Enable write_trajectory to receive trajectory_Xt-1.pdb and trajectory_pX0.pdb.
- **Timeout exceeded**: Increase the timeout input. Consider disabling trajectories, or using TEST mode for quick checks.
- **Incompatible checkpoint selection**: If results seem invalid, try Auto checkpoint or pick a checkpoint known to support your task type (unconditional, inpainting, complex).
