# ModelMergeSimple
## Documentation
- Class name: `ModelMergeSimple`
- Category: `advanced/model_merging`
- Output node: `False`

Merges two models by blending their parameters based on a specified ratio. This operation allows for the creation of a new model that inherits characteristics from both input models, with the ratio determining the influence of each model on the resulting merged model.
## Input types
### Required
- **`model1`**
    - The first model to merge. It serves as the base model onto which patches from the second model are applied.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model to merge. Its parameters are extracted as patches and applied to the first model based on the specified ratio.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`ratio`**
    - Determines the blend ratio between the two models' parameters. A ratio closer to 0 gives more weight to the first model, while a ratio closer to 1 gives more weight to the second model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting model after merging the parameters of the two input models based on the specified ratio.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,Anything Everywhere,CR Apply LoRA Stack,ModelMergeSimple,Reroute`


## Source code
```python
class ModelMergeSimple:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model1": ("MODEL",),
                              "model2": ("MODEL",),
                              "ratio": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "merge"

    CATEGORY = "advanced/model_merging"

    def merge(self, model1, model2, ratio):
        m = model1.clone()
        kp = model2.get_key_patches("diffusion_model.")
        for k in kp:
            m.add_patches({k: kp[k]}, 1.0 - ratio, ratio)
        return (m, )

```
