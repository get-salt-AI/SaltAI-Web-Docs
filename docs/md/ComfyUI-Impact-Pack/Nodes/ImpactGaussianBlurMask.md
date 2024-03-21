# Gaussian Blur Mask
## Documentation
- Class name: `ImpactGaussianBlurMask`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies a Gaussian blur to a given mask. It adjusts the mask's sharpness by using a specified kernel size and sigma value, effectively smoothing the mask's edges and features.
## Input types
### Required
- **`mask`**
    - The mask to be blurred. This is the primary target for the Gaussian blur operation, aiming to smooth its features.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`kernel_size`**
    - Determines the size of the Gaussian kernel used for blurring. A larger kernel size results in a more pronounced blur effect.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma`**
    - Controls the spread of the blur. A higher sigma value increases the radius of the blur effect, making the mask smoother.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`mask`**
    - The blurred mask resulting from the application of the Gaussian blur operation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class GaussianBlurMask:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "mask": ("MASK", ),
                     "kernel_size": ("INT", {"default": 10, "min": 0, "max": 100, "step": 1}),
                     "sigma": ("FLOAT", {"default": 10.0, "min": 0.1, "max": 100.0, "step": 0.1}),
                }}

    RETURN_TYPES = ("MASK", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, mask, kernel_size, sigma):
        mask = torch.unsqueeze(mask, dim=-1)
        mask = utils.tensor_gaussian_blur_mask(mask, kernel_size, sigma)
        mask = torch.squeeze(mask, dim=-1)
        return (mask, )

```
