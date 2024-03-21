# SEGSPaste
## Documentation
- Class name: `SEGSPaste`
- Category: `ImpactPack/Detailer`
- Output node: `False`

The SEGSPaste node is designed for blending segmented images with a reference image, applying transformations such as Gaussian blur and alpha blending to seamlessly integrate segments. It supports handling different dimensions of masks and images, ensuring compatibility and flexibility in image composition.
## Input types
### Required
- **`image`**
    - The reference image onto which the segments are pasted. It acts as the canvas for the final composite image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`segs`**
    - A collection of segmented images and associated metadata, crucial for determining how each segment is processed and integrated into the final image.
    - Python dtype: `List[Dict[str, torch.Tensor]]`
    - Comfy dtype: `SEGS`
- **`feather`**
    - The feathering value applied to the edges of the segments, enhancing the blending effect for a more natural integration.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`alpha`**
    - The alpha value used for blending the segments with the reference image, controlling the transparency of the pasted segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`ref_image_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The composite image resulting from the blending of segments onto the reference image, showcasing the integrated scene.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VHS_VideoCombine,ImageUpscaleWithModel`


## Source code
```python
class SEGSPaste:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "image": ("IMAGE", ),
                     "segs": ("SEGS", ),
                     "feather": ("INT", {"default": 5, "min": 0, "max": 100, "step": 1}),
                     "alpha": ("INT", {"default": 255, "min": 0, "max": 255, "step": 1}),
                     },
                "optional": {"ref_image_opt": ("IMAGE", ), }
                }

    RETURN_TYPES = ("IMAGE", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detailer"

    @staticmethod
    def doit(image, segs, feather, alpha=255, ref_image_opt=None):

        segs = core.segs_scale_match(segs, image.shape)

        result = None
        for i, single_image in enumerate(image):
            image_i = single_image.unsqueeze(0).clone()

            for seg in segs[1]:
                ref_image = None
                if ref_image_opt is None and seg.cropped_image is not None:
                    cropped_image = seg.cropped_image
                    if isinstance(cropped_image, np.ndarray):
                        cropped_image = torch.from_numpy(cropped_image)
                    ref_image = cropped_image[i].unsqueeze(0)
                elif ref_image_opt is not None:
                    ref_tensor = ref_image_opt[i].unsqueeze(0)
                    ref_image = crop_image(ref_tensor, seg.crop_region)
                if ref_image is not None:
                    if seg.cropped_mask.ndim == 3 and len(seg.cropped_mask) == len(image):
                        mask = seg.cropped_mask[i]
                    elif seg.cropped_mask.ndim == 3 and len(seg.cropped_mask) > 1:
                        print(f"[Impact Pack] WARN: SEGSPaste - The number of the mask batch({len(seg.cropped_mask)}) and the image batch({len(image)}) are different. Combine the mask frames and apply.")
                        combined_mask = (seg.cropped_mask[0] * 255).to(torch.uint8)

                        for frame_mask in seg.cropped_mask[1:]:
                            combined_mask |= (frame_mask * 255).to(torch.uint8)

                        combined_mask = (combined_mask/255.0).to(torch.float32)
                        mask = utils.to_binary_mask(combined_mask, 0.1)
                    else:  # ndim == 2
                        mask = seg.cropped_mask

                    mask = tensor_gaussian_blur_mask(mask, feather) * (alpha/255)
                    x, y, *_ = seg.crop_region
                    tensor_paste(image_i, ref_image, (x, y), mask)

            if result is None:
                result = image_i
            else:
                result = torch.concat((result, image_i), dim=0)

        return (result, )

```
