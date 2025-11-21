# RF Diffusion Denoiser Config

Builds a denoiser configuration dictionary to control how noise is applied during RF Diffusion sampling. It lets you independently scale translation (Cα) and rotation (frame) noise at the initial and final diffusion steps and choose how noise scales across steps (constant or linear). The result is intended to be plugged into the RF Diffusion node to fine-tune denoising behavior.

## Usage

Use this node when you need finer control over the denoising schedule in RF Diffusion. Configure the noise scales and schedules here, then connect its output to the 'denoiser_config' input of the RF Diffusion node. Typical workflows tune these parameters to stabilize sampling or encourage exploration by adjusting initial and final noise magnitudes.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Translation noise scale at the initial step (t=T) for Cα coordinates. Controls how much positional noise is injected at the start.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Translation noise scale at the final step (t=1) for Cα coordinates. Controls residual positional noise near the end.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final Cα noise scales. 'constant' keeps noise fixed; 'linear' interpolates across steps.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Rotation noise scale at the initial step (t=T) for frame orientations. Controls how much rotational noise is injected at the start.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Rotation noise scale at the final step (t=1) for frame orientations. Controls residual rotational noise near the end.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final frame (rotation) noise scales. 'constant' keeps noise fixed; 'linear' interpolates across steps.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A configuration dictionary with denoiser parameters to feed into RF Diffusion.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 0.8, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.8, 'frame_noise_schedule_type': 'linear'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Ranges**: All scale values must be between 0.0 and 1.0.
- **Schedules**: 'constant' uses the same noise at all steps; 'linear' interpolates between initial and final values.
- **Targeted usage**: This node does not perform generation; it only prepares configuration for the RF Diffusion node.
- **Selective application**: The denoiser settings are used only if this node's output is connected to the RF Diffusion node's 'denoiser_config' input.
- **Defaults**: Defaults keep both translation and rotation noise at 1.0 with 'constant' schedules.

## Troubleshooting
- **Config seems ignored**: Ensure the 'denoiser_config' output is connected to the RF Diffusion node. Unconnected configs have no effect.
- **Validation errors**: Confirm all float values are within [0.0, 1.0] and schedule types are one of ['linear', 'constant'].
- **Unstable or noisy outputs**: Reduce 'noise_scale_ca' and/or 'noise_scale_frame', or lower the final scales to taper noise more strongly near the end.
- **Too deterministic or low diversity**: Increase initial scales (noise_scale_ca/frame) or use 'linear' schedules with higher starting values.
