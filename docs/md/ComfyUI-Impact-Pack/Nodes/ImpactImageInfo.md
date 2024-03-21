# ImpactImageInfo
## Documentation
- Class name: `ImpactImageInfo`
- Category: `ImpactPack/Logic/_for_test`
- Output node: `False`

The `ImpactImageInfo` node is designed to process and enhance images for visual impact, focusing on operations such as cropping, enhancing detail, and applying segmentation masks. It utilizes a combination of models and techniques to refine image quality and detail, adaptively handling both single images and sequences for animation. The node's functionality spans from preprocessing images, including cropping and tensor conversion, to applying advanced enhancement techniques tailored for animated sequences, leveraging segmentation masks for precise adjustments.
## Input types
### Required
- **`value`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
## Output types
- **`int`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `EmptyImage,ttN pipeLoader,EmptyLatentImage`


## Source code
```python
class ImpactImageInfo:
    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
                    "value": ("IMAGE", ),
                    },
                }

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Logic/_for_test"

    RETURN_TYPES = ("INT", "INT", "INT", "INT")
    RETURN_NAMES = ("batch", "height", "width", "channel")

    def doit(self, value):
        return (value.shape[0], value.shape[1], value.shape[2], value.shape[3])

```
