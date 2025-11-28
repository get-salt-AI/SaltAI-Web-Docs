# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a denoiser configuration for RF Diffusion runs. It controls how much noise is applied to translations (C-alpha) and rotations (frame) at the start and end of the denoising process, and how those values change over the denoising schedule.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want explicit control over the denoising behavior in RF Diffusion. Configure the translation and rotation noise scales and choose a schedule type (constant or linear), then connect its output to the denoiser_config input of the RF Diffusion node. If you do not need custom denoiser behavior, you can skip this node and RF Diffusion will use its defaults.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step (t=T) translation noise scale applied to C-alpha coordinates.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step (t=1) translation noise scale applied to C-alpha coordinates.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Schedule for interpolating translation noise between initial and final values.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step (t=T) rotation noise scale (frame noise).</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step (t=1) rotation noise scale (frame noise).</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Schedule for interpolating rotation noise between initial and final values.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary for RF Diffusion denoiser containing the specified noise scales and schedules.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 0.8, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.7, 'frame_noise_schedule_type': 'linear'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Noise scale bounds**: All noise scales are constrained between 0.0 and 1.0.
- **Schedules**: 'constant' uses the same value throughout; 'linear' interpolates from the initial value to the final value over the denoising steps.
- **Pass-through behavior**: The node validates via UI constraints and returns the values as provided; no additional transformation is applied.
- **Integration**: Connect the output to the 'denoiser_config' input of the RF Diffusion node to take effect; otherwise defaults are used.
- **Translation vs rotation**: 'ca' parameters affect coordinate translations, while 'frame' parameters affect rotations.

## Troubleshooting
- **Unexpectedly weak or strong denoising**: Reduce or increase the final_noise_scale_* values. For a smoother transition, choose 'linear' instead of 'constant'.
- **No effect on results**: Ensure this node's output is connected to the RF Diffusion node's 'denoiser_config' input and that the RF Diffusion run uses that configuration.
- **Validation errors when setting values**: Keep noise scales within [0.0, 1.0] and select one of the allowed schedule types ('constant' or 'linear').
