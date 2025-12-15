# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates protein backbones using RFdiffusion, optionally conditioned on an input structure, motifs, or scaffold-guided settings. Supports full or partial diffusion, symmetry, potentials, and detailed contig mapping. Returns the designed structure and, if requested, trajectory snapshots.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to design new protein backbones either unconditionally (length-only) or conditionally (based on an input PDB and contig layout). Provide the contigs, optional conditioning configs (symmetry, contigmap, denoiser, potentials, scaffold-guided), choose a checkpoint and sampler, and set diffusion parameters. Integrate it early for generative design, then feed outputs into downstream scoring/relaxation, validation, or sequence design nodes.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">['Auto','Base','Complex_base','Complex_Fold_base','InpaintSeq','InpaintSeq_Fold','ActiveSite','Base_epoch8','Complex_beta']</td><td style="word-wrap: break-word;">Which model checkpoint to use. Auto selects an appropriate checkpoint based on inputs; manual selection requires ensuring compatibility with your task.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, returns trajectory PDBs for Xt-1 and pX0.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">['default','SelfConditioning','ScaffoldedSampler']</td><td style="word-wrap: break-word;">Sampler variant to run during diffusion.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Aligns the predicted motif to the provided motif during generation.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion stop step (1–50). Higher speed with higher values (stops earlier).</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence layout. Unconditional: length range only (e.g., 100-100). Conditional: combine fixed motifs (A10-25), flexible gaps (5-15), and chain breaks (/0). For partial diffusion (partial_T != 50), must match the exact residue counts and order of the input PDB.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residues on the target protein to involve in interactions (conditional design). Format like A30,A33,A34. Leave empty for unconditional.</td><td style="word-wrap: break-word;">A30,A33,A34</td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timestep to start denoising from (0–50). Use 50 to disable (i.e., full diffusion). Values <50 enable partial diffusion and require an input PDB and fully specified contigs matching it.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base random seed used for deterministic design runs.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">['MOCK','PROD','TEST']</td><td style="word-wrap: break-word;">Execution mode. MOCK uses a predefined demo result. PROD runs the service with your parameters. TEST reduces compute (shorter run, no trajectory, non-cautious) for quick checks.</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the diffusion process (30–12000).</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional starting PDB for conditional or partial diffusion. Required for partial diffusion (partial_T < 50).</td><td style="word-wrap: break-word;">{'filename': 'input_structure.pdb', 'data': '<pdb-bytes-or-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Symmetry configuration. Produce via RF Diffusion Symmetry Config node.</td><td style="word-wrap: break-word;">{'symmetry': 'c2', 'recenter': True, 'radius': 10, 'model_only_neighbors': False, 'symmetric_self_cond': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Contig mapping and masking settings. Produce via RF Diffusion Contigmap Config node.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A10-25', 'inpaint_str': 'B165-178', 'provide_seq': '172-177,200-205', 'length': '100-150'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Noise schedule and scaling for translations/rotations. Produce via RF Diffusion Denoiser Config node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0, 'ca_noise_schedule_type': 'constant', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 1.0, 'frame_noise_schedule_type': 'constant'}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Guiding potentials (contact/shape constraints, substrates, oligomer settings). Produce via RF Diffusion Potentials Config node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B', 'substrate': 'HEM'}</td></tr>
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td>False</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Scaffold-guided or fold-conditioning settings (secondary structure files, target PDB for PPI). Produce via RF Diffusion Scaffold Guided Config node.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'target_pdb': False, 'scaffold_dir': '/path/to/scaffolds', 'sampled_insertion': 5, 'mask_loops': True}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">The generated protein backbone structure.</td><td style="word-wrap: break-word;">{'filename': 'generation.pdb', 'data': '<pdb-bytes-or-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot for Xt-1 (returned when write_trajectory is true; may be empty in TEST mode or when disabled).</td><td style="word-wrap: break-word;">{'filename': 'trajectory_Xt-1.pdb', 'data': '<pdb-bytes-or-text>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot for pX0 (returned when write_trajectory is true; may be empty in TEST mode or when disabled).</td><td style="word-wrap: break-word;">{'filename': 'trajectory_pX0.pdb', 'data': '<pdb-bytes-or-text>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unconditional vs Conditional**: Unconditional design requires no input_pdb, contigs must be length-only (e.g., 100-100), hotspot_res must be empty, and contigmap_config fields (except length) must be empty.
- **Partial diffusion requires input**: If partial_T < 50 (partial diffusion), you must provide input_pdb and a fully specified contig layout matching the input PDB residue counts and chain order.
- **Conditional contigs**: For conditional sampling with full diffusion (partial_T = 50), contigs must include at least one fixed region with chain letters (e.g., A10-25).
- **Checkpoint compatibility**: Manual checkpoints may not support all input configurations. Auto is recommended unless you know the correct model for your setup.
- **Modes**: TEST mode forces faster settings (final_step=49, write_trajectory=false, cautious=false). MOCK mode returns precomputed demo data.
- **Trajectory outputs**: If write_trajectory is false, trajectory outputs may be empty.
- **Timeouts**: Complex conditional runs with trajectories may require a higher timeout.

## Troubleshooting
- **Error: Non empty `contigs` must be passed**: Provide valid contigs. For unconditional, use length-only (e.g., 120-120). For conditional, include fixed regions (e.g., A10-25).
- **Error: For partial diffusion sampling, expected `input_pdb` to be provided**: Set partial_T=50 to disable partial diffusion or supply input_pdb.
- **Error: Unconditional settings invalid**: Remove hotspot_res, and ensure contigmap_config fields other than length are empty when input_pdb is not provided.
- **Error: For conditional sampling, contigs missing fixed region**: Include at least one chain-lettered fixed segment (e.g., A10-25).
- **Trajectory not returned**: Ensure write_trajectory=true and avoid TEST mode; increase timeout for large jobs.
- **Generation times out**: Increase the timeout input; reduce complexity (shorter contigs, disable trajectory, use TEST for debugging).
- **Unexpected geometry or poor contacts**: Use potentials_config to add guiding potentials, adjust guide_scale/decay, or refine contigmap/denoiser settings.
- **Symmetry not applied**: Verify symmetry_config fields and values (e.g., c2, d5, tetrahedral) and ensure they fit your design goal.
