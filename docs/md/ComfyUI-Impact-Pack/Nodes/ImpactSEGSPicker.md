# Picker (SEGS)
## Documentation
- Class name: `ImpactSEGSPicker`
- Category: `ImpactPack/Util`
- Output node: `True`

The ImpactSEGSPicker node processes segmentation data to generate candidate images based on provided segmentations and an optional fallback image. It applies a mask to each segmentation, adjusts the mask values, and optionally crops images from the fallback image if no cropped image is available. The node also allows for the selection of specific segments based on user input, filtering the segments accordingly.
## Input types
### Required
- **`picks`**
    - A comma-separated string of indices representing the user's selection of specific segments. This input is crucial for filtering the segments to only include those selected by the user.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`segs`**
    - The segmentation data to be processed. This includes both the segmentation masks and any associated cropped images.
    - Python dtype: `Tuple[Any, List[Segment]]`
    - Comfy dtype: `SEGS`
### Optional
- **`fallback_image_opt`**
    - An optional fallback image that can be used to crop images for segments that do not have an associated cropped image.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`segs`**
    - The filtered segmentation data, including only the segments selected by the user.
    - Python dtype: `Tuple[Any, List[Segment]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SEGSPicker:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "picks": ("STRING", {"multiline": True, "dynamicPrompts": False, "pysssss.autocomplete": False}),
                    "segs": ("SEGS",),
                    },
                "optional": {
                     "fallback_image_opt": ("IMAGE", ),
                    },
                "hidden": {"unique_id": "UNIQUE_ID"},
                }

    RETURN_TYPES = ("SEGS", )

    OUTPUT_NODE = True

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, picks, segs, fallback_image_opt=None, unique_id=None):
        if fallback_image_opt is not None:
            segs = core.segs_scale_match(segs, fallback_image_opt.shape)

        # generate candidates image
        cands = []
        for seg in segs[1]:
            if seg.cropped_image is not None:
                cropped_image = seg.cropped_image
            elif fallback_image_opt is not None:
                # take from original image
                cropped_image = crop_image(fallback_image_opt, seg.crop_region)
            else:
                cropped_image = empty_pil_tensor()

            mask_array = seg.cropped_mask
            mask_array[mask_array < 0.3] = 0.3
            mask_array = mask_array[None, ..., None]
            cropped_image = cropped_image * mask_array

            cands.append(cropped_image)

        impact.impact_server.segs_picker_map[unique_id] = cands

        # pass only selected
        pick_ids = set()

        for pick in picks.split(","):
            try:
                pick_ids.add(int(pick)-1)
            except Exception:
                pass

        new_segs = []
        for i in pick_ids:
            if 0 <= i < len(segs[1]):
                new_segs.append(segs[1][i])

        return ((segs[0], new_segs),)

```
