# Stable Diffusion - Core

Generates images from text prompts using the Stable Diffusion Core model, supporting basic prompt-to-image workflows with essential configuration options.

<img src="/images/nodes/stable-diffusion/stable-diffusion-core.png" alt="Stable Diffusion - Core" class="rounded-lg">

## Quick Start

1. Select the Stable Diffusion - Core node in your workflow.
2. Provide a text prompt and adjust required settings.
3. Run the node to generate images.

## Setup Guide

### 1. Add the Node
1. Open your node editor.
2. Drag the Stable Diffusion - Core node into your workspace.

### 2. Configure Inputs
1. Enter your desired prompt and negative prompt.
2. Set aspect ratio, style, seed, batch size, and image strength as needed.

### 3. Generate Images
1. Connect the node and execute the workflow.
2. Retrieve generated images from the node output.

## Basic Usage

### Text-to-Image Generation
* Input a descriptive prompt to generate an image.
* Adjust style, aspect ratio, and batch size for different outputs.
* Use negative prompts to avoid unwanted elements.

## Configuration

### Required Inputs
| Field           | Description                                 | Type   | Example         |
|----------------|---------------------------------------------|--------|----------------|
| model          | Model identifier for generation             | string | "sd-core"      |
| aspect_ratio   | Output image aspect ratio                   | string | "1:1"          |
| prompt         | Main text prompt for image generation       | string | "A sunset"     |
| negative_prompt| Text to avoid in the image                  | string | "blurry"       |
| style_preset   | Style preset for the image                  | string | "photographic" |
| seed           | Random seed for reproducibility             | int    | 42             |
| batch_size     | Number of images to generate                | int    | 1              |
| image_strength | Strength of image guidance (0-1)            | float  | 0.5            |
| cfg_scale      | Classifier-free guidance scale              | float  | 7.5            |

### Optional Inputs
*None*

### Outputs
| Field   | Description                | Example         |
|---------|----------------------------|-----------------|
| image   | Generated image tensor      | <tensor object> |

## Best Practices

### Prompt Engineering
* Use clear, descriptive prompts for best results.
* Experiment with style presets to achieve different visual effects.

### Performance & Quality
* Adjust batch size for faster or bulk generation.
* Set a fixed seed for reproducible outputs.

## Troubleshooting

### Common Issues
* **Images not as expected**: Refine your prompt or adjust style/negative prompts.
* **Generation fails**: Ensure all required fields are filled and valid.

### Need Help?
* Check the node documentation for configuration examples.
* Review logs for error messages during generation.
