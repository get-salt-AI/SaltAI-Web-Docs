# RF Diffusion Denoiser Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node builds a denoiser configuration object for RFdiffusion that controls how much noise is applied to Cα translations and backbone frame rotations during the diffusion process. It exposes initial and final noise scales and schedule types for both translations and rotations, letting you tune the balance between structural exploration and convergence. The resulting configuration is passed into the main RF Diffusion node to fine-tune sampling behavior.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusiondenoiserconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want explicit control over RFdiffusion’s denoising behavior instead of relying only on default settings. It is typically paired with the main RF Diffusion node (SaaSRFDiffusionNode) to influence how far designs move from their starting structures and how tightly they converge by the final steps.

Typical workflow position:
1) Configure the main generation in RF Diffusion (SaaSRFDiffusionNode) with checkpoint, contigs, partial_T, and seeds.
2) Optionally configure other aspects with RF Diffusion Symmetry Config (SaaSRFDiffusionSymmetryConfigNode), RF Diffusion Contigmap Config (SaaSRFDiffusionContigmapConfigNode), RF Diffusion Potentials Config (SaaSRFDiffusionPotentialsConfigNode), and RF Diffusion Scaffold Guided Config (SaaSRFDiffusionScaffoldGuidedConfigNode).
3) Add this node to define denoiser parameters and connect its denoiser_config output into the denoiser_config input of the RF Diffusion node.

Use cases:
- Tight refinement around a known motif or scaffold: reduce translation and rotation noise (for example 0.3–0.5) to avoid drifting away from structural constraints.
- Exploratory monomer or binder design: keep initial noise near 1.0 and optionally reduce it toward the end with a linear schedule to explore broadly then converge.

Best practices:
- Start from defaults (all noise scales 1.0, schedules set to constant), then change one parameter at a time to see its effect.
- For sensitive conditional tasks, use higher initial noise but noticeably lower final noise with a linear schedule for smoother convergence.
- Always connect the output only to SaaSRFDiffusionNode; it is not meant as a general-purpose config for other nodes.

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
<tr><td style="word-wrap: break-word;">noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Scaling factor for noise applied to Cα translations at the initial diffusion step (t = T). Must be between 0.0 and 1.0. Lower values reduce positional perturbation at the start, while higher values allow more aggressive exploration of backbone positions.</td><td style="word-wrap: break-word;">0.8</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_ca</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Scaling factor for noise applied to Cα translations at the final denoising step (t = 1). Must be between 0.0 and 1.0. Lower values tighten positional convergence at the end; equal to noise_scale_ca with a constant schedule keeps translation noise unchanged through the trajectory.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">ca_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">["linear", "constant"]</td><td style="word-wrap: break-word;">Interpolation schedule between initial (noise_scale_ca) and final (final_noise_scale_ca) Cα translation noise. Use constant to keep the same scale across all timesteps, or linear to interpolate linearly from initial to final scale.</td><td style="word-wrap: break-word;">linear</td></tr>
<tr><td style="word-wrap: break-word;">noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Scaling factor for noise applied to backbone frame rotations at the initial diffusion step (t = T). Must be between 0.0 and 1.0. Controls how much rotational perturbation is introduced at the beginning of sampling.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">final_noise_scale_frame</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Scaling factor for noise applied to backbone frame rotations at the final denoising step (t = 1). Must be between 0.0 and 1.0. Lower values enforce tighter rotational convergence; combined with a linear schedule this allows rotational noise to anneal over time.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">frame_noise_schedule_type</td><td>True</td><td style="word-wrap: break-word;">["linear", "constant"]</td><td style="word-wrap: break-word;">Interpolation schedule between initial (noise_scale_frame) and final (final_noise_scale_frame) rotation noise. constant keeps rotation noise fixed at the initial value, while linear gradually changes it across timesteps.</td><td style="word-wrap: break-word;">constant</td></tr>
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
<tr><td style="word-wrap: break-word;">denoiser_config</td><td style="word-wrap: break-word;">RFDIFFUSION_DENOISER_CONFIG</td><td style="word-wrap: break-word;">Denoiser configuration object containing all specified parameters in a dictionary-like structure. It includes keys for Cα translation noise scales, frame rotation noise scales, and their schedule types. This output is intended to be connected directly to the denoiser_config input of the RF Diffusion node (SaaSRFDiffusionNode) to control denoising behavior during protein generation.</td><td style="word-wrap: break-word;">{"noise_scale_ca": 0.8, "final_noise_scale_ca": 0.3, "ca_noise_schedule_type": "linear", "noise_scale_frame": 1.0, "final_noise_scale_frame": 0.5, "frame_noise_schedule_type": "constant"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node is lightweight; it only constructs a small configuration object and has negligible runtime or memory overhead compared with RF Diffusion itself.
- **Limitations**: All noise scale values are constrained to the [0.0, 1.0] range; you cannot request noise outside this interval or exceed the model’s inherent noise levels.
- **Behavior**: The node does not enforce consistency between initial and final scales; equal values with a linear schedule behave effectively like a constant schedule but are still allowed.
- **Behavior**: The denoiser configuration only affects stochastic denoising; structural constraints from symmetry, contigmap, potentials, and scaffold-guided configurations still strongly shape the final protein structures.

## Troubleshooting
- **Common Error 1**: UI or validation rejects noise values as out of range. Ensure all FLOAT fields (noise_scale_ca, final_noise_scale_ca, noise_scale_frame, final_noise_scale_frame) are between 0.0 and 1.0 inclusive.
- **Common Error 2**: RF Diffusion outputs look almost identical to the input scaffold or motif. This often indicates that noise scales are set too low (for example near 0.0). Increase noise_scale_ca and noise_scale_frame, and consider using a linear schedule with a higher initial noise value.
- **Common Error 3**: Generated backbones appear unstable or unrealistic. Very high noise scales (near 1.0 for both initial and final) across all steps can over-randomize structures. Try reducing final_noise_scale_ca and final_noise_scale_frame and using a linear schedule so that noise decays toward the last steps.
