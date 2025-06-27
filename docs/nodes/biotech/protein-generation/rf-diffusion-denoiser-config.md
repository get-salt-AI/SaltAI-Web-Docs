# RF Diffusion Denoiser Config

Provides interface for passing denoiser configuration parameters to the RF Diffusion node.

<img src="/images/nodes/biotech/protein-generation/rf-diffusion-denoiser-config.png" alt="RF Diffusion Denoiser Config" class="rounded-lg">

## Quick Start

1. Add the RF Diffusion Denoiser Config node to your workflow.
2. Set the noise and schedule parameters as needed for your design.
3. Connect the output to the `denoiser_config` input of the RF Diffusion node.

## Setup Guide

### 1. Insert Node
1. Drag the RF Diffusion Denoiser Config node into your workflow.
2. Connect it to the RF Diffusion node where advanced denoiser configuration is required.

### 2. Configure Parameters
1. Adjust noise scales and schedule types for CA and frame as needed.
2. Save and run the workflow.

## Basic Usage

### Customizing Denoiser Settings
* Use to fine-tune noise parameters for translation and rotation during diffusion.
* Useful for advanced users optimizing generation quality or speed.

## Configuration

### Required Inputs
| Field                  | Description                                                                 | Type    | Example |
|------------------------|-----------------------------------------------------------------------------|---------|---------|
| noise_scale_ca         | Scales the amount of noise to add to translations at the initial (t=T) step. | FLOAT   | 1.0     |
| final_noise_scale_ca   | Scales the noise for translations at the final (t=1) step.                   | FLOAT   | 1.0     |
| ca_noise_schedule_type | Function to interpolate between noiseT and noise1 for translations.          | COMBO   | constant|
| noise_scale_frame      | Scales the noise for rotations at the initial (t=T) step.                    | FLOAT   | 1.0     |
| final_noise_scale_frame| Scales the noise for rotations at the final (t=1) step.                      | FLOAT   | 1.0     |
| frame_noise_schedule_type| Function to interpolate between noiseT and noise1 for rotations.            | COMBO   | constant|

### Optional Inputs
*None*

### Outputs
| Field           | Description                                 | Example |
|-----------------|---------------------------------------------|---------|
| denoiser_config | Configuration dict for RF Diffusion node.   | {"noise_scale_ca":1.0,...} |

## Best Practices

### Parameter Tuning
* Start with default values for most use cases.
* Increase noise scales for more diversity, decrease for more stability.

### Workflow Integration
* Only use this node if you need to override default denoiser settings in RF Diffusion.
* Connect directly to the `denoiser_config` input of the RF Diffusion node.

## Troubleshooting

### Common Issues
* **Unexpected results:** Try reverting to default parameter values.
* **Node not connected:** Ensure the output is linked to the correct RF Diffusion input.

### Need Help?
* See the [RF Diffusion documentation](../protein-generation/rf-diffusion.md) for integration examples.
* For further support, visit the SaltAI community or documentation portal.
