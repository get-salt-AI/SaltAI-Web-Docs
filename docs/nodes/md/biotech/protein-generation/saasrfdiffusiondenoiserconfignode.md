# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a denoiser configuration for RF Diffusion, controlling how much noise is applied to coordinates and frames during the diffusion steps. Lets you set initial and final noise scales and choose the interpolation schedule for both translation (Cα) and rotation (frame) noise.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to create a denoiser_config JSON and connect it to the 'RF Diffusion' node's denoiser_config input. Tune the noise scales to balance exploration (higher noise) vs. stability (lower noise), and pick a schedule (constant or linear) for how noise changes from the first to the final step.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Amount of translation (Cα) noise at the initial step (t=T). 0.0–1.0 where higher values add more noise.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Amount of translation (Cα) noise at the final step (t=1). 0.0–1.0 where higher values add more noise.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Interpolation schedule for translation (Cα) noise between initial and final scales. Allowed values: 'linear', 'constant'.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Amount of rotation (frame) noise at the initial step (t=T). 0.0–1.0 where higher values add more noise.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Amount of rotation (frame) noise at the final step (t=1). 0.0–1.0 where higher values add more noise.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Interpolation schedule for rotation (frame) noise between initial and final scales. Allowed values: 'linear', 'constant'.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A configuration dictionary for the denoiser component, to be fed into the RF Diffusion node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 0.8, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.8, 'frame_noise_schedule_type': 'linear'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Value ranges**: All noise scale fields must be between 0.0 and 1.0.
- **Schedules**: 'constant' keeps the same noise throughout; 'linear' interpolates from initial to final values across steps.
- **Integration**: This node only affects generation if its JSON output is connected to the RF Diffusion node's denoiser_config input.
- **Tuning guidance**: Lowering final noise scales can stabilize late steps; too-low values may reduce diversity or cause suboptimal designs.

## Troubleshooting
- **Config not applied**: Ensure the denoiser_config output is connected to the RF Diffusion node and that the run uses this connection.
- **Invalid value errors**: Keep all noise scales within 0.0–1.0 and schedules to 'linear' or 'constant'.
- **Results too noisy or unstable**: Decrease noise_scale_* and/or use 'linear' schedules with lower final_* values.
- **Results lack diversity**: Increase initial noise_scale_* or use 'constant' schedules to maintain higher noise throughout.
