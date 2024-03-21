# SAMDetector (combined)
## Documentation
- Class name: `SAMDetectorCombined`
- Category: `ImpactPack/Detector`
- Output node: `False`

This node combines various detection techniques to generate a mask for an image. It utilizes a SAM model along with segmentation data, image details, and various parameters like detection hints, dilation, threshold, and more to produce a precise mask.
## Input types
### Required
- **`sam_model`**
    - The SAM model used for generating the mask. It's a crucial component that directly influences the mask's accuracy and detail.
    - Python dtype: `str`
    - Comfy dtype: `SAM_MODEL`
- **`segs`**
    - Segmentation data that provides additional context for mask generation, enhancing the accuracy of the output.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
- **`image`**
    - The input image for which the mask is to be generated. It serves as the primary source of visual data for the process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`detection_hint`**
    - Hints to guide the detection process, allowing for more tailored and effective mask generation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`dilation`**
    - The amount of dilation applied to the mask, affecting its size and the inclusion of surrounding areas.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`threshold`**
    - The threshold value for mask generation, determining the sensitivity of the detection.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_expansion`**
    - Expands the bounding box around detected objects, potentially including more of the object or its context in the mask.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`mask_hint_threshold`**
    - Threshold for applying mask hints, influencing how hints are used to refine the mask.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`mask_hint_use_negative`**
    - Determines how negative spaces within the mask hints are treated, affecting the final mask's detail and accuracy.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`mask`**
    - The generated mask based on the input image and parameters. It highlights the areas of interest as determined by the detection process.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImpactSegsAndMask,Segs & Mask,MaskToImage`


## Source code
```python
class SAMDetectorCombined:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "sam_model": ("SAM_MODEL", ),
                        "segs": ("SEGS", ),
                        "image": ("IMAGE", ),
                        "detection_hint": (["center-1", "horizontal-2", "vertical-2", "rect-4", "diamond-4", "mask-area",
                                            "mask-points", "mask-point-bbox", "none"],),
                        "dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                        "threshold": ("FLOAT", {"default": 0.93, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),
                        "mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "mask_hint_use_negative": (["False", "Small", "Outter"], )
                      }
                }

    RETURN_TYPES = ("MASK",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    def doit(self, sam_model, segs, image, detection_hint, dilation,
             threshold, bbox_expansion, mask_hint_threshold, mask_hint_use_negative):
        return (core.make_sam_mask(sam_model, segs, image, detection_hint, dilation,
                                   threshold, bbox_expansion, mask_hint_threshold, mask_hint_use_negative), )

```
