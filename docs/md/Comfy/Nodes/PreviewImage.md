# Preview Image
## Documentation
- Class name: `PreviewImage`
- Category: `image`
- Output node: `True`

The `PreviewImage` node is designed for creating temporary preview images. It generates a unique temporary file name for each image, allows for image compression, and saves the images in a temporary directory.
## Input types
### Required
- **`images`**
    - The 'images' parameter is crucial as it directly influences the node's core functionality, which is to process and save temporary preview images. The quality and characteristics of the input images significantly affect the output.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class PreviewImage(SaveImage):
    def __init__(self):
        self.output_dir = folder_paths.get_temp_directory()
        self.type = "temp"
        self.prefix_append = "_temp_" + ''.join(random.choice("abcdefghijklmnopqrstupvxyz") for x in range(5))
        self.compress_level = 1

    @classmethod
    def INPUT_TYPES(s):
        return {"required":
                    {"images": ("IMAGE", ), },
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }

```
