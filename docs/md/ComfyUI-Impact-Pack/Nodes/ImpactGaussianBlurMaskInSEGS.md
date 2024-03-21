# Gaussian Blur Mask (SEGS)
## Documentation
- Class name: `ImpactGaussianBlurMaskInSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

This node applies a Gaussian blur to the masks within a collection of segmentation elements (SEGS). It iterates over each segmentation element, blurs its cropped mask using specified kernel size and sigma values, and then constructs a new set of segmentation elements with the blurred masks. This process is useful for smoothing the edges of masks, potentially improving the visual quality or the performance of subsequent operations that are sensitive to mask precision.
## Input types
### Required
- **`segs`**
    - The collection of segmentation elements (SEGS) to which the Gaussian blur will be applied. Each element's mask is blurred individually.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
- **`kernel_size`**
    - The size of the Gaussian kernel used for blurring. A larger kernel size results in more blurring.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sigma`**
    - The standard deviation of the Gaussian kernel. Controls the extent of blurring; a higher sigma value results in more blurring.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`segs`**
    - The collection of segmentation elements (SEGS) with their masks blurred using the Gaussian blur operation.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class GaussianBlurMaskInSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     "kernel_size": ("INT", {"default": 10, "min": 0, "max": 100, "step": 1}),
                     "sigma": ("FLOAT", {"default": 10.0, "min": 0.1, "max": 100.0, "step": 0.1}),
                }}

    RETURN_TYPES = ("SEGS", )

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, kernel_size, sigma):
        new_segs = []
        for seg in segs[1]:
            mask = utils.tensor_gaussian_blur_mask(seg.cropped_mask, kernel_size, sigma)
            mask = torch.squeeze(mask, dim=-1).squeeze(0).numpy()
            seg = SEG(seg.cropped_image, mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
            new_segs.append(seg)

        return ((segs[0], new_segs), )

```
