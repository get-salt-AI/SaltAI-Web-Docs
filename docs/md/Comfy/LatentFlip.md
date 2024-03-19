# Flip Latent
## Documentation
- Class name: `LatentFlip`
- Category: `latent/transform`
- Output node: `False`

The `LatentFlip` node performs a flip transformation on latent representations of images. Depending on the specified method, it can flip the images either vertically or horizontally. This operation is useful for data augmentation or to correct the orientation of images in the latent space.
## Input types
### Required
- **`samples`**
    - The latent representation of images to be flipped. This input is crucial as it directly determines the data that will undergo the transformation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`flip_method`**
    - Specifies the axis along which the images in the latent space will be flipped. This parameter controls the direction of the flip, allowing for vertical or horizontal flipping.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`latent`**
    - The flipped latent representation of images. This output is the result of applying the specified flip transformation to the input latent images.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentFlip:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "flip_method": (["x-axis: vertically", "y-axis: horizontally"],),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "flip"

    CATEGORY = "latent/transform"

    def flip(self, samples, flip_method):
        s = samples.copy()
        if flip_method.startswith("x"):
            s["samples"] = torch.flip(samples["samples"], dims=[2])
        elif flip_method.startswith("y"):
            s["samples"] = torch.flip(samples["samples"], dims=[3])

        return (s,)

```
