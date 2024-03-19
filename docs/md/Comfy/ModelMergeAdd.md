# ModelMergeAdd
## Documentation
- Class name: `ModelMergeAdd`
- Category: `advanced/model_merging`
- Output node: `False`

Merges two models by cloning the first model and adding key patches from the second model. This process involves copying the first model and then applying modifications from the second model to the cloned version, effectively merging their characteristics.
## Input types
### Required
- **`model1`**
    - The base model to be cloned and merged with the second model. It serves as the starting point for the merging process.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The model from which key patches are extracted and added to the cloned first model. It contributes its unique characteristics to the merged model.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
## Output types
- **`model`**
    - The resulting model after merging the key patches from the second model into the cloned first model. It combines the characteristics of both input models.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ModelAdd:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model1": ("MODEL",),
                              "model2": ("MODEL",),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "merge"

    CATEGORY = "advanced/model_merging"

    def merge(self, model1, model2):
        m = model1.clone()
        kp = model2.get_key_patches("diffusion_model.")
        for k in kp:
            m.add_patches({k: kp[k]}, 1.0, 1.0)
        return (m, )

```
