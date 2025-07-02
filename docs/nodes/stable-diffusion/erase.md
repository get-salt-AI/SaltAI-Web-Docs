# Erase

Removes selected regions from an input image using a mask, producing a clean background in the erased area.

<img src="/images/nodes/stable-diffusion/erase.png" alt="Erase" class="rounded-lg">

## Quick Start

1. Prepare your input image and a mask highlighting the area to erase.
2. Run the node to generate an image with the masked region erased.

## Setup Guide

### 1. Prepare Inputs
1. Obtain or create the image you want to edit.
2. Create a mask image where the area to erase is marked.

### 2. Configure Node
1. Upload the image and mask.
2. Set the batch size and seed as needed.

## Basic Usage

### Erasing Unwanted Objects
* Use a mask to select unwanted objects or regions.
* The node will erase the masked area, filling it with a clean background.
* Useful for removing text, people, or artifacts from images.

## Configuration

### Required Inputs
| Field         | Description                        | Type   | Example         |
|---------------|------------------------------------|--------|-----------------|
| image         | Input image to process             | image  | (uploaded file) |
| mask          | Mask image for erase region        | image  | (uploaded file) |
| seed          | Random seed for reproducibility    | int    | 42              |
| batch_size    | Number of images to generate       | int    | 1               |

### Optional Inputs
*None*

### Outputs
| Field  | Description                | Example         |
|--------|----------------------------|-----------------|
| image  | Image with region erased   | (output image)  |

## Best Practices

### Mask Preparation
* Use a precise mask to target only the area you want to erase.
* Avoid overly large masks for best results.

### Image Quality
* Use high-resolution images for cleaner erasure.
* Adjust the seed for different output variations.

## Troubleshooting

### Common Issues
* **Output is not as expected**: Check that the mask accurately covers the area to erase and that the input image is clear.
* **Node fails to run**: Ensure all required fields are filled and the image/mask sizes are supported.

### Need Help?
* Consult the platform documentation for node usage tips.
* Check community forums for similar issues and solutions.
