# Merge Mask Batches 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_MergeMasks`
- Category: `Video Helper Suite 🎥🅥🅗🅢/mask`
- Output node: `False`

This node is designed to merge two mask tensors based on a specified merge strategy, potentially resizing one to match the other's dimensions. It supports different strategies for deciding which mask to resize (e.g., based on size or a specific preference for one of the masks) and allows for the application of scaling methods and cropping to achieve the desired alignment and size.
## Input types
### Required
- **`mask_A`**
    - The first mask tensor to be merged. Its dimensions may be adjusted based on the merge strategy and scaling method to align with the second mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`mask_B`**
    - The second mask tensor to be merged. Depending on the merge strategy and scaling method, its dimensions may be adjusted to align with the first mask.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`merge_strategy`**
    - Determines the strategy for aligning and potentially resizing the masks before merging. It influences which mask is used as a template or whether the smaller or larger mask dictates the final size.
    - Python dtype: `MergeStrategies`
    - Comfy dtype: `ENUM`
- **`scale_method`**
    - Specifies the method used for scaling the masks during the merge process. This affects the quality and approach to resizing.
    - Python dtype: `ScaleMethods`
    - Comfy dtype: `ENUM`
- **`crop`**
    - Defines the cropping method to be applied after scaling, ensuring the merged mask fits the desired dimensions.
    - Python dtype: `CropMethods`
    - Comfy dtype: `ENUM`
## Output types
- **`mask`**
    - The result of merging the two input masks into a single tensor.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`int`**
    - The total number of masks in the merged tensor, indicating the size of the batch.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MergeMasks:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "mask_A": ("MASK",),
                "mask_B": ("MASK",),
                "merge_strategy": (MergeStrategies.list_all,),
                "scale_method": (ScaleMethods.list_all,),
                "crop": (CropMethods.list_all,),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/mask"

    RETURN_TYPES = ("MASK", "INT",)
    RETURN_NAMES = ("MASK", "count",)
    FUNCTION = "merge"

    def merge(self, mask_A: Tensor, mask_B: Tensor, merge_strategy: str, scale_method: str, crop: str):
        masks = []
        # if not same dimensions, do scaling
        if mask_A.shape[2] != mask_B.shape[2] or mask_A.shape[1] != mask_B.shape[1]:
            A_size = mask_A.shape[2] * mask_A.shape[1]
            B_size = mask_B.shape[2] * mask_B.shape[1]
            # determine which to use
            use_A_as_template = True
            if merge_strategy == MergeStrategies.MATCH_A:
                pass
            elif merge_strategy == MergeStrategies.MATCH_B:
                use_A_as_template = False
            elif merge_strategy in (MergeStrategies.MATCH_SMALLER, MergeStrategies.MATCH_LARGER):
                if A_size <= B_size:
                    use_A_as_template = True if merge_strategy == MergeStrategies.MATCH_SMALLER else False
            # add dimension where image channels would be expected to work with common_upscale
            mask_A = torch.unsqueeze(mask_A, 1)
            mask_B = torch.unsqueeze(mask_B, 1)
            # apply scaling
            if use_A_as_template:
                mask_B = comfy.utils.common_upscale(mask_B, mask_A.shape[3], mask_A.shape[2], scale_method, crop)
            else:
                mask_A = comfy.utils.common_upscale(mask_A, mask_B.shape[3], mask_B.shape[2], scale_method, crop)
            # undo dimension increase
            mask_A = torch.squeeze(mask_A, 1)
            mask_B = torch.squeeze(mask_B, 1)

        masks.append(mask_A)
        masks.append(mask_B)
        all_masks = torch.cat(masks, dim=0)
        return (all_masks, all_masks.size(0),)

```
