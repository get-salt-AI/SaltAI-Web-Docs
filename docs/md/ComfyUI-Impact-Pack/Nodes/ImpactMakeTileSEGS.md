# Make Tile SEGS
## Documentation
- Class name: `ImpactMakeTileSEGS`
- Category: `ImpactPack/__for_testing`
- Output node: `False`

The `ImpactMakeTileSEGS` node is designed to process segmentation masks (SEGS) by dividing them into smaller, overlapping tiles based on specified bounding box sizes and overlap parameters. It adjusts overlap and bounding box sizes if necessary, handles irregularities in masks, and can filter in or out specific segments. This node is crucial for handling large images or masks by breaking them down into manageable pieces for further processing or analysis.
## Input types
### Required
- **`images`**
    - The images to be processed and divided into tiles. This parameter is crucial for defining the source material that will undergo the tiling operation.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`bbox_size`**
    - Specifies the size of the bounding box for tiling. It determines how large each tile will be, playing a crucial role in dividing the image or mask into smaller segments for detailed analysis or processing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Determines the factor by which the bounding box is enlarged or reduced during the cropping process. This affects the size of the tiles and their coverage area.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`min_overlap`**
    - Defines the minimum overlap between tiles. This parameter ensures that there is sufficient overlap between adjacent tiles to maintain continuity and avoid missing or duplicating areas during processing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`filter_segs_dilation`**
    - Specifies the dilation amount for the segments to be filtered out. This parameter helps in adjusting the size of the exclusion zones around the specified segments, ensuring more precise control over what is excluded from the tiles.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`mask_irregularity`**
    - Controls the irregularity of the mask edges. A higher value leads to more irregular, non-uniform edges, which can be useful for certain types of analysis or when trying to simulate more naturalistic segmentation boundaries.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`irregular_mask_mode`**
    - Determines the mode for generating irregular masks, offering options between reusing masks for speed, quality, or generating all masks randomly for each tile. This affects the computational efficiency and quality of the generated tiles.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
### Optional
- **`filter_in_segs_opt`**
    - Optional parameter to specify segments that should be included in the tiling process. This allows for focusing the analysis or processing on specific areas or features within the image or mask.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
- **`filter_out_segs_opt`**
    - Optional parameter to specify segments that should be excluded from the tiling process. This allows for the removal of unwanted areas or features from the processed tiles.
    - Python dtype: `List[SEG]`
    - Comfy dtype: `SEGS`
## Output types
- **`segs`**
    - The output is a collection of segmentation masks (SEGS) generated from the original image or mask. Each SEGS represents a smaller segment of the original, processed according to the specified parameters.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SEGSPreview,ImpactSEGSConcat`


## Source code
```python
class MakeTileSEGS:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "images": ("IMAGE", ),
                     "bbox_size": ("INT", {"default": 512, "min": 64, "max": 4096, "step": 8}),
                     "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 10, "step": 0.1}),
                     "min_overlap": ("INT", {"default": 5, "min": 0, "max": 512, "step": 1}),
                     "filter_segs_dilation": ("INT", {"default": 20, "min": -255, "max": 255, "step": 1}),
                     "mask_irregularity": ("FLOAT", {"default": 0, "min": 0, "max": 1.0, "step": 0.01}),
                     "irregular_mask_mode": (["Reuse fast", "Reuse quality", "All random fast", "All random quality"],)
                    },
                "optional": {
                    "filter_in_segs_opt": ("SEGS", ),
                    "filter_out_segs_opt": ("SEGS", ),
                    }
                }

    RETURN_TYPES = ("SEGS",)

    FUNCTION = "doit"

    CATEGORY = "ImpactPack/__for_testing"

    def doit(self, images, bbox_size, crop_factor, min_overlap, filter_segs_dilation, mask_irregularity=0, irregular_mask_mode="Reuse fast", filter_in_segs_opt=None, filter_out_segs_opt=None):
        if bbox_size <= 2*min_overlap:
            new_min_overlap = 2 / bbox_size
            print(f"[MakeTileSEGS] min_overlap should be greater than bbox_size. (value changed: {min_overlap} => {new_min_overlap})")
            min_overlap = new_min_overlap

        _, ih, iw, _ = images.size()

        mask_cache = None
        mask_quality = 512
        if mask_irregularity > 0:
            if irregular_mask_mode == "Reuse fast":
                mask_quality = 128
                mask_cache = np.zeros((128, 128)).astype(np.float32)
                core.random_mask(mask_cache, (0, 0, 128, 128), factor=mask_irregularity, size=mask_quality)
            elif irregular_mask_mode == "Reuse quality":
                mask_quality = 512
                mask_cache = np.zeros((512, 512)).astype(np.float32)
                core.random_mask(mask_cache, (0, 0, 512, 512), factor=mask_irregularity, size=mask_quality)
            elif irregular_mask_mode == "All random fast":
                mask_quality = 512

        # create exclusion mask
        if filter_out_segs_opt is not None:
            exclusion_mask = core.segs_to_combined_mask(filter_out_segs_opt)
            exclusion_mask = utils.make_3d_mask(exclusion_mask)
            exclusion_mask = utils.resize_mask(exclusion_mask, (ih, iw))
            exclusion_mask = dilate_mask(exclusion_mask.cpu().numpy(), filter_segs_dilation)
        else:
            exclusion_mask = None

        if filter_in_segs_opt is not None:
            and_mask = core.segs_to_combined_mask(filter_in_segs_opt)
            and_mask = utils.make_3d_mask(and_mask)
            and_mask = utils.resize_mask(and_mask, (ih, iw))
            and_mask = dilate_mask(and_mask.cpu().numpy(), filter_segs_dilation)

            a, b = core.mask_to_segs(and_mask, True, 1.0, False, 0)
            if len(b) == 0:
                return a, b

            start_x, start_y, c, d = b[0].crop_region
            w = c - start_x
            h = d - start_y
        else:
            start_x = 0
            start_y = 0
            h, w = ih, iw
            and_mask = None

        # calculate tile factors
        if bbox_size > h or bbox_size > w:
            new_bbox_size = min(bbox_size, min(w, h))
            print(f"[MaskTileSEGS] bbox_size is greater than resolution (value changed: {bbox_size} => {new_bbox_size}")
            bbox_size = new_bbox_size

        n_horizontal = int(w / (bbox_size - min_overlap))
        n_vertical = int(h / (bbox_size - min_overlap))

        w_overlap_sum = (bbox_size * n_horizontal) - w
        if w_overlap_sum < 0:
            n_horizontal += 1
            w_overlap_sum = (bbox_size * n_horizontal) - w

        w_overlap_size = 0 if n_horizontal == 1 else int(w_overlap_sum/(n_horizontal-1))

        h_overlap_sum = (bbox_size * n_vertical) - h
        if h_overlap_sum < 0:
            n_vertical += 1
            h_overlap_sum = (bbox_size * n_vertical) - h

        h_overlap_size = 0 if n_vertical == 1 else int(h_overlap_sum/(n_vertical-1))

        new_segs = []

        y = start_y
        for j in range(0, n_vertical):
            x = start_x
            for i in range(0, n_horizontal):
                x1 = x
                y1 = y

                if x+bbox_size < iw-1:
                    x2 = x+bbox_size
                else:
                    x2 = iw
                    x1 = iw-bbox_size

                if y+bbox_size < ih-1:
                    y2 = y+bbox_size
                else:
                    y2 = ih
                    y1 = ih-bbox_size

                bbox = x1, y1, x2, y2
                crop_region = make_crop_region(iw, ih, bbox, crop_factor)
                cx1, cy1, cx2, cy2 = crop_region

                mask = np.zeros((cy2 - cy1, cx2 - cx1)).astype(np.float32)

                rel_left = x1 - cx1
                rel_top = y1 - cy1
                rel_right = x2 - cx1
                rel_bot = y2 - cy1

                if mask_irregularity > 0:
                    if mask_cache is not None:
                        core.adaptive_mask_paste(mask, mask_cache, (rel_left, rel_top, rel_right, rel_bot))
                    else:
                        core.random_mask(mask, (rel_left, rel_top, rel_right, rel_bot), factor=mask_irregularity, size=mask_quality)

                    # corner filling
                    if rel_left == 0:
                        pad = int((x2 - x1) / 8)
                        mask[rel_top:rel_bot, :pad] = 1.0

                    if rel_top == 0:
                        pad = int((y2 - y1) / 8)
                        mask[:pad, rel_left:rel_right] = 1.0

                    if rel_right == mask.shape[1]:
                        pad = int((x2 - x1) / 8)
                        mask[rel_top:rel_bot, -pad:] = 1.0

                    if rel_bot == mask.shape[0]:
                        pad = int((y2 - y1) / 8)
                        mask[-pad:, rel_left:rel_right] = 1.0
                else:
                    mask[rel_top:rel_bot, rel_left:rel_right] = 1.0

                mask = torch.tensor(mask)

                if exclusion_mask is not None:
                    exclusion_mask_cropped = exclusion_mask[cy1:cy2, cx1:cx2]
                    mask[exclusion_mask_cropped != 0] = 0.0

                if and_mask is not None:
                    and_mask_cropped = and_mask[cy1:cy2, cx1:cx2]
                    mask[and_mask_cropped == 0] = 0.0

                is_mask_zero = torch.all(mask == 0.0).item()

                if not is_mask_zero:
                    item = SEG(None, mask.numpy(), 1.0, crop_region, bbox, "", None)
                    new_segs.append(item)

                x += bbox_size - w_overlap_size
            y += bbox_size - h_overlap_size

        res = (ih, iw), new_segs  # segs
        return (res,)

```
