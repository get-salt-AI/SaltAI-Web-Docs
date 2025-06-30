# Remove Background

Removes the background from an input image, isolating the main subject for further processing or export.

<img src="/images/nodes/stable-diffusion/remove-background.png" alt="Remove Background" class="rounded-lg">

## Quick Start

1. Prepare your input image.
2. Select the Remove Background node in your workflow.
3. Connect your image input and run the node to obtain a background-free result.

## Setup Guide

### 1. Add the Node
1. Open your node editor.
2. Locate and add the "Remove Background" node to your workspace.

### 2. Connect Inputs
1. Connect your image input to the node.
2. Configure required parameters as needed.

### 3. Run the Node
1. Execute the workflow to process the image and remove its background.

## Basic Usage

### Isolate Subject from Image
* Use to extract the main subject from a photo for compositing or editing.
* Ideal for preparing images for presentations, design, or further AI processing.

## Configuration

### Required Inputs
| Field         | Description                        | Type         | Example         |
|--------------|------------------------------------|--------------|----------------|
| image        | Input image tensor                 | torch.Tensor | (image tensor) |
| mask         | Optional mask to guide removal     | torch.Tensor | (mask tensor)  |
| batch_size   | Number of images to process        | int          | 1              |

### Optional Inputs
*None*

### Outputs
| Field | Description                | Example         |
|-------|----------------------------|----------------|
| image | Image with background removed | (image tensor) |

## Best Practices

### Image Preparation
* Use high-quality images for best results.
* Ensure the subject is clearly distinguishable from the background.

### Workflow Integration
* Combine with other nodes for advanced editing.
* Batch process multiple images for efficiency.

## Troubleshooting

### Common Issues
* **Output is not as expected**: Check that the input image is clear and the subject is distinct from the background.
* **Processing fails for large images**: Ensure the image does not exceed the maximum supported size (4,194,304 pixels).

### Need Help?
* Consult platform documentation for node usage guidance.
* Verify input formats and node connections in your workflow.
