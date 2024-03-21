# Merge Image Batches 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_MergeImages`
- Category: `Video Helper Suite 🎥🅥🅗🅢/image`
- Output node: `False`

The `VHS_MergeImages` node is designed to merge two batches of images into a single batch. It supports various strategies for handling differing image sizes, including matching the size of one batch to the other, scaling to the smaller or larger dimensions, and applying specific scaling and cropping methods. This functionality is crucial for tasks that require uniform image dimensions, such as batch processing in machine learning models.
## Input types
### Required
- **`images_A`**
    - The first batch of images to be merged. Its dimensions may be adjusted based on the merge strategy to match the second batch.
    - Python dtype: `Tensor`
    - Comfy dtype: `IMAGE`
- **`images_B`**
    - The second batch of images to be merged. Its dimensions may be adjusted based on the merge strategy to match the first batch.
    - Python dtype: `Tensor`
    - Comfy dtype: `IMAGE`
- **`merge_strategy`**
    - Determines how the dimensions of the two image batches are aligned. Options include matching the dimensions of one batch to the other, or scaling to the smaller or larger dimensions.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scale_method`**
    - Specifies the method used for scaling images when adjusting dimensions, such as bilinear or bicubic interpolation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`crop`**
    - Defines how images are cropped when adjusting dimensions, with options like center cropping or no cropping.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`image`**
    - The merged batch of images, with uniform dimensions as determined by the merge strategy.
    - Python dtype: `Tensor`
    - Comfy dtype: `IMAGE`
- **`int`**
    - The total number of images in the merged batch.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MergeImages:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "images_A": ("IMAGE",),
                "images_B": ("IMAGE",),
                "merge_strategy": (MergeStrategies.list_all,),
                "scale_method": (ScaleMethods.list_all,),
                "crop": (CropMethods.list_all,),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/image"

    RETURN_TYPES = ("IMAGE", "INT",)
    RETURN_NAMES = ("IMAGE", "count",)
    FUNCTION = "merge"

    def merge(self, images_A: Tensor, images_B: Tensor, merge_strategy: str, scale_method: str, crop: str):
        images = []
        # if not same dimensions, do scaling
        if images_A.shape[3] != images_B.shape[3] or images_A.shape[2] != images_B.shape[2]:
            images_A = images_A.movedim(-1,1)
            images_B = images_B.movedim(-1,1)

            A_size = images_A.shape[3] * images_A.shape[2]
            B_size = images_B.shape[3] * images_B.shape[2]
            # determine which to use
            use_A_as_template = True
            if merge_strategy == MergeStrategies.MATCH_A:
                pass
            elif merge_strategy == MergeStrategies.MATCH_B:
                use_A_as_template = False
            elif merge_strategy in (MergeStrategies.MATCH_SMALLER, MergeStrategies.MATCH_LARGER):
                if A_size <= B_size:
                    use_A_as_template = True if merge_strategy == MergeStrategies.MATCH_SMALLER else False
            # apply scaling
            if use_A_as_template:
                images_B = comfy.utils.common_upscale(images_B, images_A.shape[3], images_A.shape[2], scale_method, crop)
            else:
                images_A = comfy.utils.common_upscale(images_A, images_B.shape[3], images_B.shape[2], scale_method, crop)
            images_A = images_A.movedim(1,-1)
            images_B = images_B.movedim(1,-1)

        images.append(images_A)
        images.append(images_B)
        all_images = torch.cat(images, dim=0)
        return (all_images, all_images.size(0),)

```
