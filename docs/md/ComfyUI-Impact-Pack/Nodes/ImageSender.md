# Image Sender
## Documentation
- Class name: `ImageSender`
- Category: `ImpactPack/Util`
- Output node: `True`

The `ImageSender` node is designed to send images over a network. It saves images using a specified prefix and optional additional information, then sends these images along with a link ID to a server. This functionality is crucial for applications requiring real-time image sharing or processing across different systems.
## Input types
### Required
- **`images`**
    - The images to be sent. This parameter is central to the node's operation as it directly influences the content that will be transmitted.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`filename_prefix`**
    - A prefix for the filenames of the images to be saved. This allows for organized storage and easier identification of images on the server side.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`link_id`**
    - An identifier used to link the sent images with a specific session or user on the server side. This ensures that the images are correctly associated upon receipt.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`ui`**
    - The user interface component displaying the sent images. This allows for immediate visual feedback within the application.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImageSender(nodes.PreviewImage):
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "images": ("IMAGE", ),
                    "filename_prefix": ("STRING", {"default": "ImgSender"}),
                    "link_id": ("INT", {"default": 0, "min": 0, "max": sys.maxsize, "step": 1}), },
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }

    OUTPUT_NODE = True

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, images, filename_prefix="ImgSender", link_id=0, prompt=None, extra_pnginfo=None):
        result = nodes.PreviewImage().save_images(images, filename_prefix, prompt, extra_pnginfo)
        PromptServer.instance.send_sync("img-send", {"link_id": link_id, "images": result['ui']['images']})
        return result

```
