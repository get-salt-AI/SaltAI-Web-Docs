# LatentCompositeMasked
## Documentation
- Class name: `LatentCompositeMasked`
- Category: `latent`
- Output node: `False`

The LatentCompositeMasked node is designed for blending two latent representations together at specified coordinates, optionally using a mask for more controlled compositing. This node enables the creation of complex latent images by overlaying parts of one image onto another, with the ability to resize the source image for a perfect fit.
## Input types
### Required
- **`destination`**
    - Comfy dtype: `LATENT`
    - The latent representation onto which another latent representation will be composited. Acts as the base layer for the composite operation.
    - Python dtype: `Dict[str, torch.Tensor]`
- **`source`**
    - Comfy dtype: `LATENT`
    - The latent representation to be composited onto the destination. This source layer can be resized and positioned according to the specified parameters.
    - Python dtype: `Dict[str, torch.Tensor]`
- **`x`**
    - Comfy dtype: `INT`
    - The x-coordinate in the destination latent representation where the source will be placed. Allows for precise positioning of the source layer.
    - Python dtype: `int`
- **`y`**
    - Comfy dtype: `INT`
    - The y-coordinate in the destination latent representation where the source will be placed, enabling accurate overlay positioning.
    - Python dtype: `int`
- **`resize_source`**
    - Comfy dtype: `BOOLEAN`
    - A boolean flag indicating whether the source latent representation should be resized to match the destination's dimensions before compositing.
    - Python dtype: `bool`
### Optional
- **`mask`**
    - Comfy dtype: `MASK`
    - An optional mask that can be used to control the blending of the source onto the destination. The mask defines which parts of the source will be visible in the final composite.
    - Python dtype: `torch.Tensor`
## Output types
- **`latent`**
    - Comfy dtype: `LATENT`
    - The resulting latent representation after compositing the source onto the destination, potentially using a mask for selective blending.
    - Python dtype: `Dict[str, torch.Tensor]`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSamplerAdvanced`


## Source code
```python
class LatentCompositeMasked:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "destination": ("LATENT",),
                "source": ("LATENT",),
                "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                "resize_source": ("BOOLEAN", {"default": False}),
            },
            "optional": {
                "mask": ("MASK",),
            }
        }
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "composite"

    CATEGORY = "latent"

    def composite(self, destination, source, x, y, resize_source, mask = None):
        output = destination.copy()
        destination = destination["samples"].clone()
        source = source["samples"]
        output["samples"] = composite(destination, source, x, y, mask, 8, resize_source)
        return (output,)

```
