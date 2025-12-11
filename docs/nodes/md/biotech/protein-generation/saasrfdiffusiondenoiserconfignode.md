# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a denoiser configuration for RF Diffusion by controlling how much noise is applied to translations (CA) and rotations (frame) at the start and end of the diffusion process. You can choose constant or linearly interpolated schedules between the initial (t=T) and final (t=1) steps to shape stability vs. exploration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to fine-tune denoising behavior when generating protein structures with RF Diffusion. Connect its output to the 'denoiser_config' input of the RF Diffusion node to adjust noise levels for translations and rotations, either keeping them constant or interpolating linearly from the start to the end of the process.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Noise scale for CA translations at the initial step (t=T). Higher values increase positional noise at the start.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Noise scale for CA translations at the final step (t=1). Controls how much noise remains at the end.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Schedule for interpolation of translation noise between initial and final steps. 'constant' keeps noise fixed; 'linear' interpolates from initial to final.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Noise scale for rotations (frame) at the initial step (t=T). Higher values increase rotational noise at the start.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Noise scale for rotations (frame) at the final step (t=1). Controls rotational noise at the end.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">['linear', 'constant']</td><td style="word-wrap: break-word;">Schedule for interpolation of rotational noise between initial and final steps. 'constant' keeps noise fixed; 'linear' interpolates from initial to final.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A configuration object containing the chosen noise scales and schedules for use by the RF Diffusion node.</td><td style="word-wrap: break-word;">{'noise_scale_ca': 1.0, 'final_noise_scale_ca': 0.8, 'ca_noise_schedule_type': 'linear', 'noise_scale_frame': 1.0, 'final_noise_scale_frame': 0.8, 'frame_noise_schedule_type': 'linear'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The noise scale inputs must be between 0.0 and 1.0 inclusive.
- Schedule types control how values transition from the initial (t=T) to the final (t=1) step: 'constant' uses the initial value throughout; 'linear' interpolates between initial and final.
- Translations (CA) and rotations (frame) are controlled independently—tune both for desired behavior.
- Connect this node's output to the 'denoiser_config' input of the RF Diffusion node; otherwise, defaults will be used.
- Higher initial noise can encourage exploration early; higher final noise may reduce final structure stability.

## Troubleshooting
- If you see unstable or distorted final structures, reduce 'final_noise_scale_ca' and 'final_noise_scale_frame', or use a 'constant' schedule with lower values.
- If results appear too conservative or stuck, increase 'noise_scale_ca' and 'noise_scale_frame' and consider a 'linear' schedule to allow more early exploration.
- If validation errors occur, verify all noise scales are within [0.0, 1.0] and schedule types are one of ['linear', 'constant'].
- If changes seem to have no effect, ensure the denoiser_config output is actually connected to the RF Diffusion node and that no downstream setting overrides it.
