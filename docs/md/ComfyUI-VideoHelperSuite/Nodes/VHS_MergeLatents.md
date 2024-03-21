# Merge Latent Batches 🎥🅥🅗🅢
## Documentation
- Class name: `VHS_MergeLatents`
- Category: `Video Helper Suite 🎥🅥🅗🅢/latent`
- Output node: `False`

The `VHS_MergeLatents` node merges two batches of latents based on a specified merge strategy, scale method, and cropping option. It adjusts the dimensions of the latents if necessary to ensure compatibility before merging them.
## Input types
### Required
- **`latents_A`**
    - The first batch of latents to be merged. It plays a crucial role in determining the final merged output, especially when the merge strategy is set to prioritize this batch.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
- **`latents_B`**
    - The second batch of latents to be merged. Depending on the merge strategy, this batch can either complement or serve as the primary basis for the merged output.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
- **`merge_strategy`**
    - Determines how the dimensions of the two latent batches are aligned before merging, affecting the final structure of the merged output.
    - Python dtype: `str`
    - Comfy dtype: `['match A', 'match B', 'match smaller', 'match larger']`
- **`scale_method`**
    - Specifies the method used for scaling latents to match dimensions, impacting the quality and appearance of the merged output.
    - Python dtype: `str`
    - Comfy dtype: `['nearest-exact', 'bilinear', 'area', 'bicubic', 'bislerp']`
- **`crop`**
    - Defines how the latents are cropped if necessary after scaling, influencing the final composition of the merged output.
    - Python dtype: `str`
    - Comfy dtype: `['disabled', 'center']`
## Output types
- **`latent`**
    - The merged batch of latents, combining elements from both input batches according to the specified strategy, scale, and crop settings.
    - Python dtype: `dict`
    - Comfy dtype: `LATENT`
- **`int`**
    - The total number of latents in the merged batch, providing a quantitative measure of the merge operation's result.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MergeLatents:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "latents_A": ("LATENT",),
                "latents_B": ("LATENT",),
                "merge_strategy": (MergeStrategies.list_all,),
                "scale_method": (ScaleMethods.list_all,),
                "crop": (CropMethods.list_all,),
            }
        }
    
    CATEGORY = "Video Helper Suite 🎥🅥🅗🅢/latent"

    RETURN_TYPES = ("LATENT", "INT",)
    RETURN_NAMES = ("LATENT", "count",)
    FUNCTION = "merge"

    def merge(self, latents_A: dict, latents_B: dict, merge_strategy: str, scale_method: str, crop: str):
        latents = []
        latents_A = latents_A.copy()["samples"]
        latents_B = latents_B.copy()["samples"]

        # if not same dimensions, do scaling
        if latents_A.shape[3] != latents_B.shape[3] or latents_A.shape[2] != latents_B.shape[2]:
            A_size = latents_A.shape[3] * latents_A.shape[2]
            B_size = latents_B.shape[3] * latents_B.shape[2]
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
                latents_B = comfy.utils.common_upscale(latents_B, latents_A.shape[3], latents_A.shape[2], scale_method, crop)
            else:
                latents_A = comfy.utils.common_upscale(latents_A, latents_B.shape[3], latents_B.shape[2], scale_method, crop)

        latents.append(latents_A)
        latents.append(latents_B)

        merged = {"samples": torch.cat(latents, dim=0)}
        return (merged, len(merged["samples"]),)

```
