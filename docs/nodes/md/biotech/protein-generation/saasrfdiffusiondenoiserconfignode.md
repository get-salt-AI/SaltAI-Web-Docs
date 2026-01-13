# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a denoiser configuration for RF Diffusion, controlling how much noise is applied during translation (C-alpha) and rotation (frame) across the diffusion steps. You can set initial and final noise levels and choose how they transition (constant or linear) to influence exploration versus stability during generation. Output connects into the RF Diffusion node to refine sampling behavior.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need finer control over the denoising schedule in RF Diffusion runs. Connect its output to the 'denoiser_config' input of the RF Diffusion node to tune how aggressively structures are perturbed at the beginning and end of the diffusion process, which can improve convergence or diversity depending on your task.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial noise scale for translations (C-alpha) at the start of denoising (t = T). Controls how much positional noise is injected early on.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final noise scale for translations (C-alpha) at the end of denoising (t = 1). Lower values enforce stability in late steps.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">How the translation noise transitions from initial to final value across steps. 'constant' keeps it fixed; 'linear' interpolates between start and end.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial noise scale for rotations (frame) at the start of denoising (t = T). Affects angular perturbations early on.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final noise scale for rotations (frame) at the end of denoising (t = 1). Lower values reduce rotational jitter in late steps.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">How the rotation noise transitions from initial to final value across steps. 'constant' keeps it fixed; 'linear' interpolates between start and end.</td><td style="word-wrap: break-word;">linear</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">Structured configuration object defining translation and rotation noise scales and schedules for RF Diffusion.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 0.5, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.5, 'frame_noise_schedule_type': 'linear'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- All noise scale values must be between 0.0 and 1.0 inclusive.
- Schedule types supported: 'constant' and 'linear'. Use 'constant' for fixed noise, 'linear' to gradually change noise through steps.
- Higher initial noise promotes exploration; lower final noise helps convergence and structural stability.
- This output is meant to be connected to the 'denoiser_config' input of the RF Diffusion node; it does nothing standalone.

## Troubleshooting
- If results look overly noisy or unstable, reduce final_noise_scale_ca and final_noise_scale_frame (e.g., to 0.2–0.5).
- If outputs lack diversity or get stuck, increase noise_scale_ca and noise_scale_frame or switch to a 'linear' schedule.
- If the node is rejected due to validation, ensure all FLOAT values are within [0.0, 1.0] and schedule types are either 'constant' or 'linear'.
- If changes seem to have little effect, confirm the 'denoiser_config' output is connected to the RF Diffusion node and not overridden by other configuration nodes.
