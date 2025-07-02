# Edit Object

Edit specific objects in an image using search-and-edit operations, allowing targeted modifications based on prompts and masks.

<img src="/images/nodes/stable-diffusion/edit-object.png" alt="Edit Object" class="rounded-lg">

## Quick Start

1. Prepare your input image and mask.
2. Specify the object to edit using the search prompt.
3. Set the edit mode (e.g., replace, remove).
4. Adjust grow mask if needed.
5. Run the node to apply the edit.

## Setup Guide

### 1. Prepare Inputs
1. Obtain or create an image and a corresponding mask highlighting the object to edit.
2. Define the search prompt describing the object.

### 2. Configure Node
1. Select the desired edit mode (e.g., replace, remove).
2. Optionally adjust the grow mask value for mask expansion.

## Basic Usage

### Replace an Object
* Use a search prompt to identify the object.
* Set edit mode to "replace" and provide a new prompt for the replacement.

### Remove an Object
* Use a search prompt to identify the object.
* Set edit mode to "remove" to erase the object from the image.

## Configuration

### Required Inputs
| Field         | Description                                 | Type   | Example           |
|---------------|---------------------------------------------|--------|-------------------|
| image         | Input image                                 | tensor | image_tensor      |
| mask          | Mask highlighting the object to edit        | tensor | mask_tensor       |
| edit_mode     | Edit operation mode (replace, remove, etc.) | string | replace           |
| search_prompt | Description of the object to edit           | string | "red apple"       |
| grow_mask     | Pixels to expand the mask                   | int    | 3                 |
| seed          | Random seed for reproducibility             | int    | 42                |
| batch_size    | Number of images to generate                | int    | 1                 |

### Optional Inputs
| Field         | Description                                 | Type   | Example           |
|---------------|---------------------------------------------|--------|-------------------|
| prompt        | Prompt for the new object/content           | string | "green apple"     |
| negative_prompt | Negative prompt to avoid unwanted content | string | "no leaves"       |

### Outputs
| Field         | Description                                 | Example           |
|---------------|---------------------------------------------|-------------------|
| image         | Edited image tensor                         | edited_image_tensor|

## Best Practices

### Prompt Engineering
* Use clear, specific search prompts to accurately target objects.
* Combine search and edit prompts for precise control.

### Mask Preparation
* Ensure the mask closely matches the object boundaries.
* Use grow_mask to slightly expand the mask if edges are missed.

## Troubleshooting

### Common Issues
* **Object not detected**: Refine the search prompt or improve the mask accuracy.
* **Unwanted areas edited**: Adjust the mask or reduce grow_mask value.

### Need Help?
* Check input image and mask alignment.
* Review prompt wording for clarity.
* Consult platform documentation for further guidance.
