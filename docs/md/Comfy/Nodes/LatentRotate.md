# Rotate Latent
## Documentation
- Class name: `LatentRotate`
- Category: `latent/transform`
- Output node: `False`

The `LatentRotate` node rotates latent representations of images by specified angles. It supports rotation by 0, 90, 180, and 270 degrees, allowing for flexible manipulation of the latent space.
## Input types
### Required
- **`samples`**
    - The latent representation of an image to be rotated. This input is crucial for defining the starting point of the rotation operation.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`rotation`**
    - Specifies the angle by which the latent image should be rotated. This parameter directly influences the orientation of the resulting latent image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`latent`**
    - The rotated latent representation of the image. This output reflects the transformation applied by the specified rotation angle.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentRotate:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "rotation": (["none", "90 degrees", "180 degrees", "270 degrees"],),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "rotate"

    CATEGORY = "latent/transform"

    def rotate(self, samples, rotation):
        s = samples.copy()
        rotate_by = 0
        if rotation.startswith("90"):
            rotate_by = 1
        elif rotation.startswith("180"):
            rotate_by = 2
        elif rotation.startswith("270"):
            rotate_by = 3

        s["samples"] = torch.rot90(samples["samples"], k=rotate_by, dims=[3, 2])
        return (s,)

```
