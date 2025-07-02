# Upscale Advanced

Enhances image resolution using advanced upscaling algorithms with support for large images and custom aspect ratios.

<img src="/images/nodes/stable-diffusion/upscale-advanced.png" alt="Upscale Advanced" class="rounded-lg">

## Quick Start

1. Add the Upscale Advanced node to your workflow.
2. Connect an input image.
3. Configure optional parameters as needed.
4. Run the workflow to obtain an upscaled image.

## Setup Guide

### 1. Add Node to Workflow
1. Open your node editor.
2. Locate and add the "Upscale Advanced" node from the node library.

### 2. Connect and Configure
1. Connect an image input.
2. Adjust parameters (e.g., aspect ratio, seed, creativity) as needed.
3. Execute the workflow.

## Basic Usage

### High-Quality Image Upscaling
* Upscale images up to 9MP (max 9437184 pixels).
* Supports aspect ratios from 0.4 to 2.5.
* Customizable creativity and seed for varied results.

## Configuration

### Required Inputs
| Field  | Description                | Type   | Example |
|--------|----------------------------|--------|---------|
| image  | Image to upscale           | Tensor | image_tensor |

### Optional Inputs
| Field           | Description                                 | Type    | Example |
|-----------------|---------------------------------------------|---------|---------|
| prompt          | Text prompt for guidance                    | String  | "A detailed landscape" |
| negative_prompt | Text prompt to avoid certain features       | String  | "blurry, low quality" |
| seed            | Random seed for reproducibility             | Integer | 42      |
| batch_size      | Number of images to process in batch        | Integer | 1       |
| creativity      | Controls variation (0.2–0.5, default 0.35) | Float   | 0.35    |

### Outputs
| Field  | Description           | Example      |
|--------|-----------------------|--------------|
| image  | Upscaled image tensor | image_tensor |

## Best Practices

### Image Preparation
* Use high-quality input images for best results.
* Ensure input image size is between 64px and 9MP.

### Parameter Tuning
* Adjust creativity for more or less variation.
* Set a fixed seed for reproducible outputs.

## Troubleshooting

### Common Issues
* **Input image too small or too large**: Ensure image size is between 64px and 9MP.
* **Aspect ratio out of range**: Use aspect ratios between 0.4 and 2.5.
* **Timeouts on large images**: For very large images, allow extra processing time (default timeout is 180s).

### Need Help?
* Check node documentation and verify input parameters.
* For persistent issues, consult your platform's support resources.
