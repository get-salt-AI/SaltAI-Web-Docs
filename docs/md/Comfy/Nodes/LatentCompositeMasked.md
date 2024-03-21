# LatentCompositeMasked
## Documentation
- Class name: `LatentCompositeMasked`
- Category: `latent`
- Output node: `False`

The `LatentCompositeMasked` node combines two latent representations (destination and source) at specified coordinates, optionally resizing the source and applying a mask to the composite operation. This node is useful for blending or overlaying features from one latent space onto another, potentially with selective masking.
## Input types
### Required
- **`destination`**
    - The latent representation onto which the source will be composited. Acts as the background for the composite operation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`source`**
    - The latent representation that will be composited onto the destination. Acts as the overlay in the composite operation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`x`**
    - The x-coordinate in the destination latent space where the source will be placed. Allows for precise positioning of the source.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate in the destination latent space where the source will be placed. Allows for precise positioning of the source.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`resize_source`**
    - Determines whether the source latent representation should be resized to match the destination's dimensions before compositing. Enables flexibility in combining latents of different sizes.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`mask`**
    - An optional mask that can be applied to the composite operation, allowing for selective blending of the source onto the destination.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`latent`**
    - The resulting latent representation after compositing the source onto the destination, potentially with resizing and masking.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
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
