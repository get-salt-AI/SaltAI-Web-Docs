# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a denoiser configuration for RF Diffusion, controlling how much noise is applied to translations (Cα positions) and rotations during the diffusion process. You can set separate initial (t=T) and final (t=1) noise scales and choose how they interpolate across steps for both translations and frames.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/RFDiffusionDenoiserConfigNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want fine-grained control over the denoising schedule in RF Diffusion. Configure translation and rotation noise scales and schedules here, then connect the output to the 'denoiser_config' input of the RF Diffusion node to influence sampling behavior.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step (t=T) noise scale for translations (Cα positions). Range 0.0–1.0.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step (t=1) noise scale for translations (Cα positions). Range 0.0–1.0.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final translation noise scales. Allowed values: linear, constant.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Initial-step (t=T) noise scale for rotations (frame noise). Range 0.0–1.0.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Final-step (t=1) noise scale for rotations (frame noise). Range 0.0–1.0.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Interpolation schedule between initial and final rotation noise scales. Allowed values: linear, constant.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A configuration dictionary with translation and rotation noise scales and schedules to feed into RF Diffusion.</td><td style="word-wrap: break-word;">{"noise_scale_ca": 1.0, "final_noise_scale_ca": 0.8, "ca_noise_schedule_type": "linear", "noise_scale_frame": 1.0, "final_noise_scale_frame": 0.8, "frame_noise_schedule_type": "linear"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Values for noise scales should be between 0.0 and 1.0; higher values inject more noise.
- Schedule type controls how values change from initial to final: constant keeps the same value; linear interpolates across steps.
- This node only constructs configuration; it has effect only when connected to the denoiser_config input of the RF Diffusion node.
- All inputs are required by this node; defaults are provided for convenience.

## Troubleshooting
- If changes have no visible effect, ensure the output is connected to the RF Diffusion node's denoiser_config input.
- If you see validation errors setting values, keep noise scales within 0.0–1.0 and use allowed schedule values: linear or constant.
- If generation quality degrades, try reducing noise scales or switch schedules from linear to constant to stabilize sampling.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-generation/RFDiffusionDenoiserConfigNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

