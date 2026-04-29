# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node builds a denoiser configuration object for RF Diffusion, controlling the noise schedule applied to C-alpha translations and rotational frames over the diffusion trajectory. You specify initial and final noise scales separately for positions and rotations, plus whether each schedule is constant or linearly interpolated. The resulting config is passed into the main RF Diffusion node to fine-tune convergence, stability, and diversity of generated protein backbones.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want finer control over the denoising schedule in RF Diffusion protein generation or design runs. It typically sits upstream of the RF Diffusion node, with its single output connected to the "denoiser_config" input of "RF Diffusion" (class: "SaaSRFDiffusionNode"). In a typical workflow, you might: (1) define backbone layout and masking using "RF Diffusion Contigmap Config", (2) optionally configure symmetry, scaffold guidance, and potentials, (3) use RF Diffusion Denoiser Config to tune how aggressively structures are perturbed at early vs late diffusion steps, and (4) feed all these configs into the main RF Diffusion node. For exploratory, diverse backbones, keep higher noise at early steps and a moderate final noise, often with linear schedules. For refinement around an input structure (partial diffusion), lower the initial and final noise scales, typically with constant schedules. Coordinate these settings with "partial_T" and "final_step" in the RF Diffusion node: larger "final_step" and stronger early noise promote exploration, while lower noise and fewer steps bias toward preservation.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Relative amount of translational noise applied to C-alpha coordinates at the initial diffusion step (t = T). Range 0.0–1.0; higher values mean stronger perturbation of atom positions at the start of the reverse process.</td><td style="word-wrap: break-word;">0.8</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Relative amount of translational noise applied to C-alpha coordinates at the final diffusion step (t = 1). Range 0.0–1.0; smaller values mean gentler updates right before output, useful for refinement-style runs.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">CATEGORY</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final C-alpha translation noise. "constant" keeps noise_scale_ca fixed across all steps; "linear" interpolates from noise_scale_ca at t = T to final_noise_scale_ca at t = 1.</td><td style="word-wrap: break-word;">linear</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Relative amount of rotational noise applied to backbone frames at the initial diffusion step (t = T). Range 0.0–1.0; higher values allow larger random reorientations of local frames early in the process.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Relative amount of rotational noise applied to backbone frames at the final diffusion step (t = 1). Range 0.0–1.0; lower values favor smoother, more stable final orientations.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">CATEGORY</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final rotational noise. "constant" keeps noise_scale_frame fixed; "linear" interpolates from noise_scale_frame at t = T to final_noise_scale_frame at t = 1.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">A configuration object (dictionary) encapsulating C-alpha and frame noise parameters and their schedules. This is consumed by the "denoiser_config" input of the RF Diffusion node to control how translation and rotation noise are applied throughout the diffusion trajectory.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 0.8, 'final_noise_scale_ca': 0.2, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.3, 'frame_noise_schedule_type': 'constant'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Adjusting the noise schedule does not significantly change runtime, but can affect how many diffusion steps you need (via RF Diffusion's "final_step") to reach good results.
- **Limitations**: All noise scale values are constrained to 0.0–1.0; extremely low values can make the model under-explore and get stuck near the starting structure, while values near 1.0 can destabilize conditional designs if combined with very small "partial_T".
- **Behavior**: The node simply packages parameters; it does not run RF Diffusion itself. Actual effects on structures only appear when the output is connected to the RF Diffusion node and a run is executed.
- **Behavior**: Translation (C-alpha) and rotation (frame) schedules are independent—using aggressive rotational noise with gentle translational noise is possible and can noticeably change backbone flexibility patterns.

## Troubleshooting
- **Outputs look almost identical to the input PDB**: Your noise scales may be too low, especially with a high "partial_T". Try increasing "noise_scale_ca" and "noise_scale_frame" to 0.7–1.0 and ensure the RF Diffusion node uses a non-trivial "final_step" (for example, 40–50).
- **Generated structures are unstable or unrealistic**: Very high noise across both translations and rotations, especially with linear schedules, can lead to noisy, unfolded backbones. Decrease "noise_scale_ca" and "noise_scale_frame" to 0.5–0.7 and/or lower "final_noise_scale_*" values.
- **Denoiser config seems to have no effect**: Verify the "denoiser_config" output is actually connected to the "denoiser_config" input of the RF Diffusion node in your workflow. Also confirm the run is not in a special mode that ignores external configs.
- **Inconsistent behavior between runs with the same seed**: Changing denoiser settings changes the diffusion trajectory, so identical seeds no longer reproduce old results. If you need strict reproducibility, keep the denoiser config fixed while varying other parameters.
