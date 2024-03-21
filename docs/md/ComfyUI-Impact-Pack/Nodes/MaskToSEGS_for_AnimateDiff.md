# MASK to SEGS for AnimateDiff
## Documentation
- Class name: `MaskToSEGS_for_AnimateDiff`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node transforms a mask into a series of segmented images (SEGS) specifically optimized for animations with differences between frames. It first converts the input mask into a 2D representation, then generates segmented images based on the provided parameters. Afterward, it combines all masks from the segmented images into a single mask and re-applies the segmentation process to ensure the final segmented images are optimized for animation differences.
## Input types
### Required
- **`mask`**
    - The input mask to be transformed into segmented images. It is the primary input that drives the segmentation process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`combined`**
    - A boolean flag indicating whether the segmented images should be combined. It affects how the segmented images are processed and optimized for animation differences.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`crop_factor`**
    - Affects the cropping of the segmented images, influencing the focus and size of the segments.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_fill`**
    - Determines whether the bounding boxes around the segments should be filled, affecting the visual representation of the segments.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`drop_size`**
    - Specifies the minimum size of segments to be included, allowing for the filtering out of smaller, potentially irrelevant segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`contour_fill`**
    - Controls whether the contours of the segments should be filled, impacting the detail level of the segmented images.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`segs`**
    - The output segmented images optimized for animations with differences between frames.
    - Python dtype: `Tuple[str, List[SEG]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MaskToSEGS_for_AnimateDiff:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                                "mask": ("MASK",),
                                "combined": ("BOOLEAN", {"default": False, "label_on": "True", "label_off": "False"}),
                                "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                                "bbox_fill": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                                "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),
                                "contour_fill": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                             }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, mask, combined, crop_factor, bbox_fill, drop_size, contour_fill=False):
        mask = make_2d_mask(mask)

        segs = core.mask_to_segs(mask, combined, crop_factor, bbox_fill, drop_size, is_contour=contour_fill)

        all_masks = SEGSToMaskList().doit(segs)[0]

        result_mask = (all_masks[0] * 255).to(torch.uint8)
        for mask in all_masks[1:]:
            result_mask |= (mask * 255).to(torch.uint8)

        result_mask = (result_mask/255.0).to(torch.float32)
        result_mask = utils.to_binary_mask(result_mask, 0.1)[0]

        return MaskToSEGS().doit(result_mask, False, crop_factor, False, drop_size, contour_fill)

```
