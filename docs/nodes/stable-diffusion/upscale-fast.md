# Upscale Fast

Quickly upscale images with minimal configuration, optimized for speed and efficiency.

<img src="/images/nodes/stable-diffusion/upscale-fast.png" alt="Upscale Fast" class="rounded-lg">

## Quick Start

1. Add the Upscale Fast node to your workflow.
2. Connect an input image.
3. Run the node to receive an upscaled output.

## Setup Guide

### 1. Add Node to Workflow
1. Open your node editor.
2. Locate and add the "Upscale Fast" node.

### 2. Connect Inputs
1. Connect the image you want to upscale to the node's input.
2. Adjust configuration as needed.

### 3. Run and Retrieve Output
1. Execute the workflow.
2. Retrieve the upscaled image from the node's output.

## Basic Usage

### Single Image Upscaling
* Use for fast upscaling of a single image.
* Minimal configuration required.

### Batch Upscaling
* Process multiple images by adjusting the batch size input.

## Configuration

### Required Inputs
| Field  | Description                  | Type   | Example        |
|--------|------------------------------|--------|----------------|
| image  | The image to upscale         | tensor | input_image    |

### Optional Inputs
*None*

### Outputs
| Field  | Description              | Example         |
|--------|--------------------------|-----------------|
| image  | The upscaled image tensor| upscaled_image  |

## Best Practices

### Image Preparation
* Use high-quality input images for best results.
* Ensure input image size is within supported range (32–1536 px per side).

### Performance
* For large batches, monitor memory usage.
* Use this node for speed; for higher quality, consider advanced upscaling nodes.

## Troubleshooting

### Common Issues
* **Input image too small or too large**: Ensure image dimensions are between 32 and 1536 pixels per side.
* **Output not as expected**: Check input image quality and try different images.

### Need Help?
* Consult the platform documentation for node usage guidance.
* Check community forums for workflow examples and troubleshooting tips.
