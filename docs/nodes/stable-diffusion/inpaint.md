# Inpaint

Inpaints masked regions of an input image using Stable Diffusion, guided by a text prompt and optional mask growth.

<img src="/images/nodes/stable-diffusion/inpaint.png" alt="Inpaint" class="rounded-lg">

## Quick Start

1. Prepare an input image and a mask indicating regions to inpaint.
2. Enter a descriptive prompt for the desired inpainted content.
3. (Optional) Adjust the grow mask value to expand the masked area.
4. Run the node to generate the inpainted image.

## Setup Guide

### 1. Prepare Inputs
1. Obtain or create an image to edit.
2. Create a mask image where white areas indicate regions to inpaint.
3. Write a prompt describing the desired result.

### 2. Configure Node
1. Set the required fields: image, mask, and prompt.
2. Optionally adjust grow mask, seed, and batch size.

### 3. Run and Review
1. Execute the node.
2. Review the output image and adjust parameters as needed.

## Basic Usage

### Inpainting an Object
* Remove unwanted objects by masking them and describing the background in the prompt.
* Restore damaged or missing parts of an image.
* Change specific regions by masking and prompting for new content.

## Configuration

### Required Inputs
| Field           | Description                                 | Type         | Example                |
|----------------|---------------------------------------------|--------------|------------------------|
| image          | Input image to inpaint                       | tensor/image | (image tensor)         |
| mask           | Mask image (white = inpaint region)          | tensor/image | (mask tensor)          |
| prompt         | Text prompt guiding inpainting               | string       | "A vase of flowers"    |

### Optional Inputs
| Field        | Description                                 | Type    | Example |
|--------------|---------------------------------------------|---------|---------|
| grow_mask    | Expand mask region by N pixels (max 100)    | int     | 10      |
| negative_prompt | Text prompt to avoid inpainting features  | string  | "blurry, distorted" |
| seed         | Random seed for reproducibility              | int     | 42      |
| batch_size   | Number of images to generate                 | int     | 1       |

### Outputs
| Field        | Description                 | Example        |
|--------------|-----------------------------|----------------|
| image        | Inpainted output image      | (image tensor) |

## Best Practices

### Prompt Engineering
* Use clear, descriptive prompts for best results.
* Specify undesired features in the negative prompt.

### Mask Preparation
* Use precise masks for targeted inpainting.
* Adjust grow mask to ensure full coverage of the region.

## Troubleshooting

### Common Issues
* **Output is not as expected**: Refine the prompt or mask for better results.
* **Masked area not fully inpainted**: Increase the grow mask value.
* **Node fails with input error**: Ensure all required fields are set and valid.

### Need Help?
* Check documentation for input formatting.
* Review example configurations for guidance.
