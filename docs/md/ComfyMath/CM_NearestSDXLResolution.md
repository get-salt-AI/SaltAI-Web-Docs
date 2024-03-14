# NearestSDXLResolution
## Documentation
- Class name: `CM_NearestSDXLResolution`
- Category: `math/graphics`
- Output node: `False`

This node finds the nearest supported SDXL resolution to the input image's resolution. It calculates the aspect ratio of the input image and selects the SDXL resolution with the closest aspect ratio, defaulting to 1024x1024 if no closer match is found.
## Input types
### Required
- **`image`**
    - The input image for which the nearest SDXL resolution is to be found. The image's aspect ratio is used to determine the closest supported SDXL resolution.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`int`**
    - The height component of the nearest SDXL resolution.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ConstrainImage|pysssss,Reroute,ImageScale`

Often used with image preprocessing to ensure the input image is compatible with SDXL model requirements by adjusting its resolution to the nearest supported SDXL resolution, ensuring optimal performance and quality in AI-driven graphics and image processing tasks.
## Source code
```python
class NearestSDXLResolution:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {"required": {"image": ("IMAGE",)}}

    RETURN_TYPES = ("INT", "INT")
    RETURN_NAMES = ("width", "height")
    FUNCTION = "op"
    CATEGORY = "math/graphics"

    def op(self, image) -> tuple[int, int]:
        image_width = image.size()[2]
        image_height = image.size()[1]
        print(f"Input image resolution: {image_width}x{image_height}")
        image_ratio = image_width / image_height
        differences = [
            (abs(image_ratio - resolution[2]), resolution)
            for resolution in SDXL_SUPPORTED_RESOLUTIONS
        ]
        smallest = None
        for difference in differences:
            if smallest is None:
                smallest = difference
            else:
                if difference[0] < smallest[0]:
                    smallest = difference
        if smallest is not None:
            width = smallest[1][0]
            height = smallest[1][1]
        else:
            width = 1024
            height = 1024
        print(f"Selected SDXL resolution: {width}x{height}")
        return (width, height)

```
