# RF Diffusion

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs RFdiffusion to generate or redesign protein structures. Supports unconditional generation (by length), conditional generation using an input PDB and contigs, and scaffold-guided/fold-conditioned design via optional configuration nodes. Can optionally output trajectory PDBs from the diffusion process and supports deterministic runs via a base seed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to create new protein backbones or redesign regions around fixed motifs. Typical workflows: (1) Unconditional generation by providing only a length range in contigs; (2) Conditional generation by providing an input PDB and contigs describing fixed and flexible regions; (3) Scaffold-guided or fold-conditioned design by connecting a Scaffold Guided Config. Optionally plug in Symmetry, Contigmap, Denoiser, and Potentials configs to control sampling behavior. Choose PROD for normal execution, TEST for fast dry-runs, or MOCK to return predefined results.

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
<tr><td style="word-wrap: break-word;">checkpoint</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which pretrained checkpoint to use. Auto automatically selects a compatible checkpoint based on inputs; others force a specific checkpoint.</td><td style="word-wrap: break-word;">Auto</td></tr>
<tr><td style="word-wrap: break-word;">write_trajectory</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include diffusion trajectory PDBs (Xt-1 and pX0) in the outputs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">model_runner</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Sampler to use during generation.</td><td style="word-wrap: break-word;">SelfConditioning</td></tr>
<tr><td style="word-wrap: break-word;">align_motif</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, aligns the model's predicted motif to the provided motif in conditional modes.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">final_step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Reverse diffusion step to stop at (1-50). Larger values reduce compute but may affect quality.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">contigs</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sequence layout specification. Unconditional: only length range (e.g., 100-100). Conditional: combine fixed regions (A10-25), flexible gaps (5-15), and chain breaks (/0). For partial diffusion (partial_T != 50), must match the exact residue order/count of input PDB.</td><td style="word-wrap: break-word;">5-15/A10-25/30-40/0 B1-100</td></tr>
<tr><td style="word-wrap: break-word;">hotspot_res</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue positions on the TARGET protein to optimize interactions against (used in PPI/binder design). Leave empty unless a target is provided via Scaffold Guided Config.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">partial_T</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Set to 50 to disable partial diffusion. Values 0-49 start denoising from a partially noised structure. Requires input_pdb when enabled.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">seed</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Base seed for deterministic generation. Derived seeds are used for each design internally.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">MOCK returns predefined data, PROD executes the service, TEST runs a faster minimal-parameter version (disables trajectory and cautious mode, sets final_step to 49).</td><td style="word-wrap: break-word;">PROD</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for the run.</td><td style="word-wrap: break-word;">600</td></tr>
<tr><td style="word-wrap: break-word;">input_pdb</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Optional initial PDB for conditional design or partial diffusion. Required for partial diffusion (partial_T != 50).</td><td style="word-wrap: break-word;">{'pdb_name': 'input_structure.pdb', 'pdb_content': '<pdb-text-or-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">symmetry_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Additional symmetry settings. Connect from 'RF Diffusion Symmetry Config'.</td><td style="word-wrap: break-word;">{'symmetry': 'c2', 'recenter': True}</td></tr>
<tr><td style="word-wrap: break-word;">contigmap_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_CONTIGMAP_CONFIG</td><td style="word-wrap: break-word;">Sequence/structure masking and length settings. Connect from 'RF Diffusion Contigmap Config'.</td><td style="word-wrap: break-word;">{'inpaint_seq': 'A10-20', 'length': '100-150'}</td></tr>
<tr><td style="word-wrap: break-word;">denoiser_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">Noise schedule customization. Connect from 'RF Diffusion Denoiser Config'.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 1.0}</td></tr>
<tr><td style="word-wrap: break-word;">potentials_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Guiding potentials to steer generation. Connect from 'RF Diffusion Potentials Config'.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15'], 'guide_scale': 10}</td></tr>
<tr><td style="word-wrap: break-word;">scaffoldguided_config</td><td>False</td><td style="word-wrap: break-word;">RFDIFFUSION_SCAFFOLDGUIDED_CONFIG</td><td style="word-wrap: break-word;">Enable scaffold-guided/fold-conditioned design and optional target PPI context. Connect from 'RF Diffusion Scaffold Guided Config'.</td><td style="word-wrap: break-word;">{'scaffoldguided': True, 'target_pdb': True}</td></tr>
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
<tr><td style="word-wrap: break-word;">generation.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Final generated or redesigned protein structure.</td><td style="word-wrap: break-word;">{'pdb_name': 'generation_0.pdb', 'pdb_content': '<pdb-text-or-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_Xt-1.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot at Xt-1 (if write_trajectory is true; disabled in TEST mode).</td><td style="word-wrap: break-word;">{'pdb_name': 'trajectory_Xt-1_0.pdb', 'pdb_content': '<pdb-text-or-reference>'}</td></tr>
<tr><td style="word-wrap: break-word;">trajectory_pX0.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Trajectory snapshot at pX0 (if write_trajectory is true; disabled in TEST mode).</td><td style="word-wrap: break-word;">{'pdb_name': 'trajectory_pX0_0.pdb', 'pdb_content': '<pdb-text-or-reference>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Contigs format rules are strict. Unconditional generation must provide only a length range (e.g., 100-100). Conditional generation must include at least one fixed region with chain letters (e.g., A10-25), unless scaffold-guided design is enabled.
- Partial diffusion: set partial_T to 50 to disable. Any value 0-49 requires input_pdb and, for non-scaffold-guided runs, contigs must match the exact residue count/order of the input PDB.
- Unconditional generation forbids hotspot_res and any contigmap_config fields other than length.
- Hotspot residues require a target protein. If hotspot_res is provided, a target must be configured via Scaffold Guided Config (target_pdb).
- TEST mode automatically sets final_step to 49, disables trajectory outputs, and turns off cautious mode for speed.
- Seeds are used deterministically; per-output seeds are derived from the base seed.
- If write_trajectory is false (or in TEST mode), trajectory outputs may be empty or not populated.

## Troubleshooting
- Error: 'Non empty contigs must be passed' or similar. Fix: For non-scaffold-guided runs, ensure contigs is provided and valid. In scaffold-guided mode, contigs may be omitted.
- Error: 'For partial diffusion sampling, expected input_pdb to be provided.' Fix: Supply input_pdb when partial_T is not 50, or set partial_T to 50 to disable partial diffusion.
- Error: 'For unconditional sampling, expected contigs to specify only length range' Fix: Use a format like 100-100 with no chain letters for unconditional mode.
- Error: 'For conditional sampling, expected contigs to specify least one fixed region' Fix: Include at least one region with a chain letter and residue range (e.g., A10-25) when not using scaffold-guided mode.
- Error: 'For unconditional sampling, expected hotspot_res to be empty' Fix: Clear hotspot_res when running unconditional generation.
- Error: 'Expected k to be empty' from contigmap settings. Fix: In unconditional mode, only length may be set in contigmap_config; clear other fields.
- Error about hotspot_res without target. Fix: Add a target protein via Scaffold Guided Config (target_pdb) or remove hotspot_res.
- No trajectory outputs returned. Fix: Ensure write_trajectory is true and not running in TEST mode; also confirm timeout is sufficient for full run.
- Timeouts. Fix: Increase timeout, reduce final_step, disable trajectory, or use TEST mode to validate configuration quickly.
