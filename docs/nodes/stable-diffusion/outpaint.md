# Outpaint

Expands an image beyond its original borders using Stable Diffusion, generating new content that blends seamlessly with the existing image.

<img src="/images/nodes/stable-diffusion/outpaint.png" alt="Outpaint" class="rounded-lg">

## Quick Start

1. Upload or select an input image.
2. Specify the number of pixels to expand in each direction (left, right, up, down).
3. Enter a prompt to guide the outpainting.
4. (Optional) Set a random seed and batch size.
5. Run the node to generate the expanded image.

## Setup Guide

### 1. Prepare Your Image
1. Ensure your image is at least 64x64 pixels.
2. Choose an image with content you want to expand.

### 2. Configure Outpainting
1. Set the number of pixels to expand for each side (left, right, up, down).
2. Provide a descriptive prompt for the new content.
3. Adjust creativity and seed as needed for variation.

## Basic Usage

### Expanding a Landscape Photo
* Expand a landscape image to create a wider scene.
* Use prompts like "mountains in the distance" or "sunset sky" for natural transitions.

### Creating Extended Portraits
* Add space above or beside a portrait for creative compositions.
* Guide the expansion with prompts such as "soft background" or "studio lighting".

## Configuration

### Required Inputs
| Field        | Description                                 | Type    | Example           |
|--------------|---------------------------------------------|---------|-------------------|
| image        | Input image to expand                       | tensor  | (image tensor)    |
| prompt       | Text prompt guiding the outpainting         | string  | "forest at dusk" |
| left         | Pixels to expand on the left                | int     | 128               |
| right        | Pixels to expand on the right               | int     | 128               |
| up           | Pixels to expand at the top                 | int     | 64                |
| down         | Pixels to expand at the bottom              | int     | 64                |

### Optional Inputs
| Field      | Description                          | Type   | Example |
|------------|--------------------------------------|--------|---------|
| creativity | Controls randomness/variation        | float  | 0.7     |
| seed       | Random seed for reproducibility      | int    | 42      |
| batch_size | Number of images to generate         | int    | 1       |

### Outputs
| Field | Description                | Example         |
|-------|----------------------------|-----------------|
| image | Outpainted image tensor(s) | (image tensor)  |

## Best Practices

### Prompt Engineering
* Use clear, descriptive prompts for best results.
* Match the prompt style to the original image content.

### Image Preparation
* Start with high-quality, uncluttered images.
* Avoid expanding images with complex, detailed edges for smoother transitions.

## Troubleshooting

### Common Issues
* **Output looks unnatural**: Refine your prompt or reduce the number of pixels expanded.
* **Node fails with input error**: Ensure all required fields are filled and at least one expansion direction is non-zero.

### Need Help?
* Check the platform documentation for node usage examples.
* Review input requirements and try different prompt variations.
