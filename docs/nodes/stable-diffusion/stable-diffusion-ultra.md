# Stable Diffusion - Ultra

A high-quality text-to-image node for generating images from prompts using the Stable Diffusion Ultra model, supporting advanced control over image size and batch generation.

<img src="/images/nodes/stable-diffusion/stable-diffusion-ultra.png" alt="Stable Diffusion - Ultra" class="rounded-lg">

## Quick Start

1. Select the Stable Diffusion - Ultra node in your workflow.
2. Provide a prompt describing the desired image.
3. Set the aspect ratio, seed, batch size, and other parameters as needed.
4. Run the node to generate images.

## Setup Guide

### 1. Node Placement
1. Add the Stable Diffusion - Ultra node to your workspace.
2. Connect required input nodes (e.g., prompt source).

### 2. Parameter Configuration
1. Adjust aspect ratio, seed, and batch size for your use case.
2. Optionally, provide a negative prompt or image for image-to-image mode.

## Basic Usage

### Text-to-Image Generation
* Generate high-quality images from text prompts.
* Control image size and batch output.

### Image-to-Image Mode
* Use an input image as guidance for generation.
* Adjust image strength for blending.

## Configuration

### Required Inputs
| Field         | Description                                 | Type   | Example        |
|--------------|---------------------------------------------|--------|----------------|
| prompt       | Text prompt describing the image to create   | string | "A sunset..."  |
| aspect_ratio | Desired aspect ratio (e.g., "1:1", "16:9") | string | "1:1"          |
| seed         | Random seed for reproducibility              | int    | 42             |
| batch_size   | Number of images to generate                 | int    | 4              |

### Optional Inputs
| Field           | Description                                         | Type         | Example        |
|-----------------|-----------------------------------------------------|--------------|----------------|
| negative_prompt | Text to avoid in the generated image                | string       | "blurry"      |
| image           | Input image for image-to-image mode                 | tensor/image | (image tensor) |
| image_strength  | Strength of guidance from input image (0-1)         | float        | 0.5            |

### Outputs
| Field   | Description                  | Example        |
|---------|------------------------------|----------------|
| image   | Generated image(s) as tensor | (image tensor) |

## Best Practices

### Prompt Engineering
* Use clear, descriptive prompts for best results.
* Experiment with different aspect ratios for varied outputs.

### Performance
* Use lower batch sizes for faster results.
* Adjust image strength for optimal image-to-image blending.

## Troubleshooting

### Common Issues
* **Images not as expected**: Refine your prompt or adjust aspect ratio.
* **Slow generation**: Reduce batch size or image resolution.

### Need Help?
* Check the node documentation for parameter details.
* Review workflow connections and input values.
