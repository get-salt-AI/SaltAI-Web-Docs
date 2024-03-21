# Crop Latent
## Documentation
- Class name: `LatentCrop`
- Category: `latent/transform`
- Output node: `False`

The `LatentCrop` node performs cropping operations on latent representations of images. It adjusts the dimensions of the given latent samples to the specified width and height, starting from the (x, y) coordinates.
## Input types
### Required
- **`samples`**
    - The latent representation of images to be cropped. This parameter is crucial for defining the input data on which the cropping operation will be performed.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`width`**
    - The desired width of the cropped latent representation. It determines how wide the output latent image will be.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - The desired height of the cropped latent representation. It affects the vertical dimension of the output latent image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`x`**
    - The x-coordinate from where the cropping should start horizontally. It defines the left boundary of the crop area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate from where the cropping should start vertically. It defines the top boundary of the crop area.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`latent`**
    - The cropped latent representation of images. This output retains the essential features of the input latent samples within the specified crop area.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LatentCrop:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT",),
                              "width": ("INT", {"default": 512, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 512, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                              "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              }}
    RETURN_TYPES = ("LATENT",)
    FUNCTION = "crop"

    CATEGORY = "latent/transform"

    def crop(self, samples, width, height, x, y):
        s = samples.copy()
        samples = samples['samples']
        x =  x // 8
        y = y // 8

        #enfonce minimum size of 64
        if x > (samples.shape[3] - 8):
            x = samples.shape[3] - 8
        if y > (samples.shape[2] - 8):
            y = samples.shape[2] - 8

        new_height = height // 8
        new_width = width // 8
        to_x = new_width + x
        to_y = new_height + y
        s['samples'] = samples[:,:,y:to_y, x:to_x]
        return (s,)

```
