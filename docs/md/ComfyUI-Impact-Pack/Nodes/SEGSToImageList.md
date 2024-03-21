# SEGSToImageList
## Documentation
- Class name: `SEGSToImageList`
- Category: `ImpactPack/Util`
- Output node: `False`

The `SEGSToImageList` node is designed to convert segmentation data into a list of images. It optionally adjusts the scale of segmentations to match a fallback image if provided. This node processes each segmentation, extracting or generating the corresponding cropped image, and compiles these images into a list. If no images are generated, a placeholder image is added to the list to ensure it's never empty.
## Input types
### Required
- **`segs`**
    - The primary input containing segmentation data. It is crucial for the operation as it provides the segments to be converted into images.
    - Python dtype: `Tuple[Any, List[SEG]]`
    - Comfy dtype: `SEGS`
### Optional
- **`fallback_image_opt`**
    - An optional image used to scale the segmentations to match its dimensions. This can influence the output images' dimensions and is useful for ensuring consistency across different inputs.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The output is a list of images derived from the input segmentations. Each image corresponds to a cropped area from the original segmentation or the fallback image, ensuring visual consistency and relevance.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SEGSToImageList:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "segs": ("SEGS", ),
                     },
                "optional": {
                     "fallback_image_opt": ("IMAGE", ),
                    }
                }

    RETURN_TYPES = ("IMAGE",)
    OUTPUT_IS_LIST = (True,)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Util"

    def doit(self, segs, fallback_image_opt=None):
        results = list()

        if fallback_image_opt is not None:
            segs = core.segs_scale_match(segs, fallback_image_opt.shape)

        for seg in segs[1]:
            if seg.cropped_image is not None:
                cropped_image = to_tensor(seg.cropped_image)
            elif fallback_image_opt is not None:
                # take from original image
                cropped_image = to_tensor(crop_image(fallback_image_opt, seg.crop_region))
            else:
                cropped_image = empty_pil_tensor()

            results.append(cropped_image)

        if len(results) == 0:
            results.append(empty_pil_tensor())

        return (results,)

```
