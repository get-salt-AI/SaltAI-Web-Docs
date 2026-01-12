# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a configuration object that adjusts the denoising noise levels and schedules for RF Diffusion runs. It controls separate noise scales for translations (Cα positions) and rotations (frames), with options to keep them constant or interpolate linearly from the initial to final step. This node only prepares settings and must be connected to the main RF Diffusion node to take effect.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want finer control over the denoising behavior of RF Diffusion, such as reducing final-step noise to refine structures or increasing initial noise to encourage broader exploration. Connect its output to the denoiser_config input of the RF Diffusion node alongside other configuration nodes like symmetry, contigmap, potentials, or scaffold-guided configs.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step translation noise scale (t=T) applied to Cα coordinates. Range 0.0–1.0.</td><td style="word-wrap: break-word;">0.8</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step translation noise scale (t=1) for Cα coordinates. Range 0.0–1.0.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Interpolation type between initial and final translation noise scales. Options: linear, constant.</td><td style="word-wrap: break-word;">linear</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step rotational noise scale (t=T) applied to frames. Range 0.0–1.0.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step rotational noise scale (t=1). Range 0.0–1.0.</td><td style="word-wrap: break-word;">0.7</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Interpolation type between initial and final rotational noise scales. Options: linear, constant.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">A denoiser configuration object to plug into the RF Diffusion node’s denoiser_config input.</td><td style="word-wrap: break-word;">noise_scale_ca=0.8, final_noise_scale_ca=0.5, ca_noise_schedule_type=linear, noise_scale_frame=1.0, final_noise_scale_frame=0.7, frame_noise_schedule_type=constant</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node does not run any generation; it only returns a configuration object to connect to the RF Diffusion node.
- All noise scale values must be between 0.0 and 1.0.
- Schedule types control how the node transitions from initial noise to final noise across denoising steps: linear interpolates, constant keeps a fixed value.
- To have an effect, ensure the output is connected to the denoiser_config input of the RF Diffusion node.
- Translation (Cα) and rotation (frame) noise scales are configured independently.

## Troubleshooting
- If adjustments have no effect: confirm the denoiser_config output is connected to the RF Diffusion node and that the run uses that connection.
- If validation errors occur: ensure all FLOAT values are within 0.0–1.0 and schedule types are one of: linear, constant.
- If structures look over-smoothed: increase final_noise_scale_ca and/or final_noise_scale_frame, or use a constant schedule.
- If structures look too noisy at the end: decrease final_noise_scale_ca and/or final_noise_scale_frame, or use a linear schedule with lower final values.
