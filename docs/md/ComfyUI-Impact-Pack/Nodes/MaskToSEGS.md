# MASK to SEGS
## Documentation
- Class name: `MaskToSEGS`
- Category: `ImpactPack/Operation`
- Output node: `False`

The `MaskToSEGS` node transforms a given mask into a series of segmented images (SEGS) based on various parameters such as whether the operation should be combined, the crop factor, whether to fill bounding boxes, the minimum size of segments to keep, and whether to fill contours. This process involves converting the mask to a 2D format, applying segmentation, and optionally adjusting the segmentation based on the provided parameters.
## Input types
### Required
- **`mask`**
    - The input mask to be transformed into segmented images. It serves as the primary input for segmentation.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`combined`**
    - Determines whether the segmentation operation should be combined into a single operation or handled separately.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`crop_factor`**
    - Adjusts the cropping of the segmented images, affecting the size and area of the segments.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_fill`**
    - Specifies whether the bounding boxes around the segments should be filled, impacting the visual representation of the segments.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`drop_size`**
    - Sets the minimum size for segments to be kept, filtering out smaller segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`contour_fill`**
    - Indicates whether the contours of the segments should be filled, altering the appearance of the segmented images.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`segs`**
    - The segmented images (SEGS) generated from the input mask, adjusted according to the specified parameters.
    - Python dtype: `Tuple[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: `DetailerForEachDebugPipe,DetailerForEachDebug`


## Source code
```python
class MaskToSEGS:
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

        result = core.mask_to_segs(mask, combined, crop_factor, bbox_fill, drop_size, is_contour=contour_fill)
        return (result, )

```
