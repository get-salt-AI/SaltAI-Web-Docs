# ModelMergeSubtract
## Documentation
- Class name: `ModelMergeSubtract`
- Category: `advanced/model_merging`
- Output node: `False`

This node performs a subtraction operation between two models by applying a multiplier to the patches extracted from the second model before subtracting them from the first model. This operation allows for the dynamic adjustment of the influence one model has over another, enabling the creation of a new model that incorporates characteristics of both input models with a specified intensity.
## Input types
### Required
- **`model1`**
    - The first model to be used in the subtraction operation. It serves as the base model from which patches of the second model are subtracted.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model from which patches are extracted and subtracted from the first model, after being scaled by the multiplier.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`multiplier`**
    - A scaling factor applied to the patches of the second model before subtraction. It controls the intensity of the subtraction effect on the resulting model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting model after subtracting scaled patches of the second model from the first model.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ModelSubtract:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model1": ("MODEL",),
                              "model2": ("MODEL",),
                              "multiplier": ("FLOAT", {"default": 1.0, "min": -10.0, "max": 10.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "merge"

    CATEGORY = "advanced/model_merging"

    def merge(self, model1, model2, multiplier):
        m = model1.clone()
        kp = model2.get_key_patches("diffusion_model.")
        for k in kp:
            m.add_patches({k: kp[k]}, - multiplier, multiplier)
        return (m, )

```
