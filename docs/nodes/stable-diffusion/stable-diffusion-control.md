# Stable Diffusion - Control

A node for applying advanced control techniques (e.g., ControlNet, style transfer) to Stable Diffusion image generation, allowing precise guidance using an input image and control type.

<img src="/images/nodes/stable-diffusion/stable-diffusion-control.png" alt="Stable Diffusion - Control" class="rounded-lg">

## Quick Start

1. Connect your input image and specify the control type (e.g., style, pose).
2. Set the prompt and negative prompt for image generation.
3. Adjust control strength and other parameters as needed.
4. Run the node to generate controlled outputs.

## Setup Guide

### 1. Prerequisites
1. Ensure Stable Diffusion and required control models are installed in your environment.
2. Prepare an input image for guidance.

### 2. Node Configuration
1. Select the control type (e.g., style, pose).
2. Fill in prompts and adjust parameters for your use case.

## Basic Usage

### Style Transfer
* Use an input image and set control type to "style" for style-guided generation.
* Adjust control strength to balance fidelity and creativity.

### Pose or Other Controls
* Set control type to "pose" or other supported types for pose-guided or custom control.
* The output will follow the structure of the input image.

## Configuration

### Required Inputs
| Field           | Description                                 | Type    | Example           |
|-----------------|---------------------------------------------|---------|-------------------|
| image           | Input image for control guidance            | tensor  | [image tensor]    |
| control_type    | Type of control (e.g., style, pose)         | string  | style             |
| prompt          | Text prompt for image generation            | string  | "A sunny beach"   |
| negative_prompt | Negative prompt to avoid unwanted features  | string  | "blurry, dark"    |
| aspect_ratio    | Desired aspect ratio (for style only)       | string  | "16:9"            |
| seed            | Random seed for reproducibility             | int     | 42                |
| batch_size      | Number of images to generate                | int     | 1                 |
| control_strength| Degree of control influence (0-1)           | float   | 0.7               |

### Optional Inputs
*None*

### Outputs
| Field        | Description                        | Example         |
|--------------|------------------------------------|-----------------|
| image        | Generated image(s) as tensor       | [image tensor]  |

## Best Practices

### Control Type Selection
* Choose the control type that matches your guidance image (e.g., use "style" for style transfer).
* For non-style controls, aspect ratio is ignored and the output matches the input image resolution.

### Parameter Tuning
* Adjust control strength for desired balance between guidance and creativity.
* Use consistent seeds for reproducible results.

## Troubleshooting

### Common Issues
* **Output not matching guidance image:** Ensure the control type matches the input image's content and use an appropriate control strength.
* **Aspect ratio ignored:** For non-style controls, aspect ratio is not applied; output will match input image resolution.

### Need Help?
* Check your model installation and input formats.
* Review logs for warnings about parameter usage.
