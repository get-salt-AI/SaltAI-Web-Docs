# Set Default Image for SEGS
## Documentation
- Class name: `SetDefaultImageForSEGS`
- Category: `ImpactPack/Util`
- Output node: `False`

This node is designed to set a default image for each segmentation (SEG) in a collection of segmentations (SEGS). It likely involves adjusting the SEGS data structure to include a default or fallback image, ensuring that each SEG within SEGS has an associated image. This operation is crucial for subsequent processing steps that require each SEG to have an image, especially in scenarios where some SEGS might be missing this information.
## Input types
### Required
- **`segs`**
    - The collection of segmentations (SEGS) for which a default image needs to be set. This parameter is essential for ensuring that each segmentation has an associated image, which is critical for further processing steps.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`override`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`segs`**
    - The updated collection of segmentations (SEGS), where each SEG now has an associated default or fallback image. This ensures that all segmentations are ready for further processing steps that require an image.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class DefaultImageForSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "segs": ("SEGS", ),
                    "image": ("IMAGE", ),
                    "override": ("BOOLEAN", {"default": True}),
                }}

    RETURN_TYPES = ("SEGS", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, image, override):
        results = []

        segs = core.segs_scale_match(segs, image.shape)

        if len(segs[1]) > 0:
            if segs[1][0].cropped_image is not None:
                batch_count = len(segs[1][0].cropped_image)
            else:
                batch_count = len(image)

            for seg in segs[1]:
                if seg.cropped_image is not None and not override:
                    cropped_image = seg.cropped_image
                else:
                    cropped_image = None
                    for i in range(0, batch_count):
                        # take from original image
                        ref_image = image[i].unsqueeze(0)
                        cropped_image2 = crop_image(ref_image, seg.crop_region)

                        if cropped_image is None:
                            cropped_image = cropped_image2
                        else:
                            cropped_image = torch.cat((cropped_image, cropped_image2), dim=0)

                new_seg = SEG(cropped_image, seg.cropped_mask, seg.confidence, seg.crop_region, seg.bbox, seg.label, seg.control_net_wrapper)
                results.append(new_seg)

            return ((segs[0], results), )
        else:
            return (segs, )

```
