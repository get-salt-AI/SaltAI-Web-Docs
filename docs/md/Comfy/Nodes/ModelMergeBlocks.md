# ModelMergeBlocks
## Documentation
- Class name: `ModelMergeBlocks`
- Category: `advanced/model_merging`
- Output node: `False`

This node merges two models by blending their components based on specified ratios. It allows for fine-tuned control over how different parts of the models are combined, making it useful for creating hybrid models with characteristics of both input models.
## Input types
### Required
- **`model1`**
    - The first model to be merged. It serves as the base model which will be cloned and modified by adding patches from the second model.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`model2`**
    - The second model from which key patches are extracted and merged into the first model based on specified ratios.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
- **`input`**
    - A default ratio for merging model components not explicitly specified by other parameters. It affects the overall blending of the two models.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`middle`**
    - Specifies the blending ratio for components in the middle of the model structure.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`out`**
    - Determines the blending ratio for the output components of the model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The resulting merged model, incorporating elements from both input models according to the specified ratios.
    - Python dtype: `comfy.model_base.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: `KSampler,CR Load LoRA`


## Source code
```python
class ModelMergeBlocks:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model1": ("MODEL",),
                              "model2": ("MODEL",),
                              "input": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                              "middle": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                              "out": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01})
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "merge"

    CATEGORY = "advanced/model_merging"

    def merge(self, model1, model2, **kwargs):
        m = model1.clone()
        kp = model2.get_key_patches("diffusion_model.")
        default_ratio = next(iter(kwargs.values()))

        for k in kp:
            ratio = default_ratio
            k_unet = k[len("diffusion_model."):]

            last_arg_size = 0
            for arg in kwargs:
                if k_unet.startswith(arg) and last_arg_size < len(arg):
                    ratio = kwargs[arg]
                    last_arg_size = len(arg)

            m.add_patches({k: kp[k]}, 1.0 - ratio, ratio)
        return (m, )

```
