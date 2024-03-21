# VividSharpen
## Documentation
- Class name: `VividSharpen`
- Category: `image/postprocessing`
- Output node: `False`

The VividSharpen node applies a vivid sharpening effect to images. It enhances image details by applying a vivid light and overlay blend based on the specified radius and strength of the sharpening effect.
## Input types
### Required
- **`images`**
    - The images to be sharpened. This input is crucial for determining the visual quality and detail enhancement of the output images.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`radius`**
    - Defines the radius of the Gaussian blur applied during the sharpening process. A larger radius results in a more pronounced sharpening effect. The radius directly influences the extent of the blur effect, which is a critical factor in achieving the desired vivid sharpening outcome.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`strength`**
    - Controls the intensity of the sharpening effect. A higher strength value leads to a more vivid and pronounced sharpening. The strength parameter adjusts the blending level of the sharpened layer with the original image, affecting the overall sharpness and clarity.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The sharpened images, enhanced with vivid light and overlay effects for improved detail and visual quality.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The VividSharpen node is primarily utilized for enhancing the visual quality of images by applying a vivid sharpening effect, which is achieved through adjusting the radius and strength parameters. It takes images as input and outputs images with improved detail and clarity, making it ideal for post-processing tasks in image enhancement pipelines.
## Source code
```python
class VividSharpen:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "images": ("IMAGE",),
                "radius": ("FLOAT", {"default": 1.5, "min": 0.01, "max": 64.0, "step": 0.01}),
                "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    RETURN_NAMES = ("images",)

    FUNCTION = "sharpen"

    CATEGORY = "image/postprocessing"

    def sharpen(self, images, radius, strength):
    
        results = []
        if images.size(0) > 1:
            for image in images:
                image = tensor2pil(image)
                results.append(pil2tensor(vivid_sharpen(image, radius=radius, strength=strength)))
            results = torch.cat(results, dim=0)
        else:
            results = pil2tensor(vivid_sharpen(tensor2pil(images), radius=radius, strength=strength))
            
        return (results,)

```
