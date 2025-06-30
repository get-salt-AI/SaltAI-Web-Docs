# Stable Diffusion - Advanced

Generates high-quality images from text prompts using advanced Stable Diffusion 3.0/3.5 models, supporting custom prompts, negative prompts, aspect ratios, and more.

<img src="/images/nodes/stable-diffusion/stable-diffusion-advanced.png" alt="Stable Diffusion - Advanced" class="rounded-lg">

## Quick Start

1. Select the "Stable Diffusion - Advanced" node in your workflow.
2. Enter your desired prompt and negative prompt.
3. Configure aspect ratio, seed, batch size, style preset, and other parameters as needed.
4. Run the node to generate images.

## Setup Guide

### 1. Node Placement
1. Add the node to your workspace from the node library.
2. Connect required inputs (prompt, aspect ratio, etc.).

### 2. Parameter Configuration
1. Adjust parameters for your use case (e.g., batch size, seed).
2. Optionally set a style preset for stylistic control.

## Basic Usage

### Text-to-Image Generation
* Generate images from descriptive text prompts.
* Use negative prompts to avoid unwanted elements.
* Adjust aspect ratio and batch size for output customization.

## Configuration

### Required Inputs
| Field           | Description                                 | Type   | Example         |
|----------------|---------------------------------------------|--------|-----------------|
| prompt         | Text describing the desired image            | string | "A sunset beach" |
| negative_prompt| Text describing what to avoid in the image   | string | "blurry, dark"  |
| aspect_ratio   | Output image aspect ratio (e.g., 1:1, 16:9)  | string | "1:1"           |
| seed           | Random seed for reproducibility              | int    | 42              |
| batch_size     | Number of images to generate                 | int    | 1               |
| style_preset   | Style preset for image generation            | string | "photographic"  |

### Optional Inputs
| Field | Description | Type | Example |
|-------|-------------|------|---------|
*None*

### Outputs
| Field | Description | Example |
|-------|-------------|---------|
| images | Generated images as tensors | [Tensor] |

## Best Practices

### Prompt Engineering
* Use clear, descriptive prompts for best results.
* Specify negative prompts to filter out unwanted features.

### Parameter Tuning
* Adjust batch size and seed for reproducibility and variety.
* Experiment with style presets for different artistic effects.

## Troubleshooting

### Common Issues
* **Images not as expected**: Refine your prompt and negative prompt for clarity.
* **Output size incorrect**: Check aspect ratio and batch size settings.

### Need Help?
* Consult the platform documentation for node usage examples.
* Verify your input values and try different parameters for improved results.
