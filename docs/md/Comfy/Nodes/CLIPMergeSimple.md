# CLIPMergeSimple
## Documentation
- Class name: `CLIPMergeSimple`
- Category: `advanced/model_merging`
- Output node: `False`

The `CLIPMergeSimple` node merges two CLIP models based on a specified ratio, excluding specific parameters related to position IDs and logit scale. This operation allows for the combination of features from both models, potentially enhancing their capabilities or creating a new model with a balanced set of characteristics from the input models.
## Input types
### Required
- **`clip1`**
    - The first CLIP model to be merged. It serves as the base model for the merging process.
    - Python dtype: `comfy.model_base.CLIPModel`
    - Comfy dtype: `CLIP`
- **`clip2`**
    - The second CLIP model to be merged. Its key patches, except for those related to position IDs and logit scale, are added to the first model based on the specified ratio.
    - Python dtype: `comfy.model_base.CLIPModel`
    - Comfy dtype: `CLIP`
- **`ratio`**
    - Determines the proportion of features from the second model to be merged into the first model. A ratio of 1 means fully adopting the second model's features, while 0 means no adoption.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`clip`**
    - The resulting CLIP model after merging the specified models according to the given ratio.
    - Python dtype: `comfy.model_base.CLIPModel`
    - Comfy dtype: `CLIP`
## Usage tips
- Infra type: `GPU`
- Common nodes: `CR Apply LoRA Stack,CLIPSetLastLayer`


## Source code
```python
class CLIPMergeSimple:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip1": ("CLIP",),
                              "clip2": ("CLIP",),
                              "ratio": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("CLIP",)
    FUNCTION = "merge"

    CATEGORY = "advanced/model_merging"

    def merge(self, clip1, clip2, ratio):
        m = clip1.clone()
        kp = clip2.get_key_patches()
        for k in kp:
            if k.endswith(".position_ids") or k.endswith(".logit_scale"):
                continue
            m.add_patches({k: kp[k]}, 1.0 - ratio, ratio)
        return (m, )

```
