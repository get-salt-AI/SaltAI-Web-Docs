# ModelMergeBlocks
## Documentation
- Class name: `ModelMergeBlocks`
- Category: `advanced/model_merging`
- Output node: `False`

ModelMergeBlocks is designed for advanced model merging operations, allowing for the integration of two models with customizable blending ratios for different parts of the models. This node facilitates the creation of hybrid models by selectively merging components from two source models based on specified parameters.
## Input types
### Required
- **`model1`**
    - Comfy dtype: `MODEL`
    - The first model to be merged. It serves as the base model onto which patches from the second model are applied.
    - Python dtype: `comfy.model_base.Model`
- **`model2`**
    - Comfy dtype: `MODEL`
    - The second model from which patches are extracted and applied to the first model, based on the specified blending ratios.
    - Python dtype: `comfy.model_base.Model`
- **`input`**
    - Comfy dtype: `FLOAT`
    - Specifies the blending ratio for the input layer of the models. It determines how much of the second model's input layer is merged into the first model.
    - Python dtype: `float`
- **`middle`**
    - Comfy dtype: `FLOAT`
    - Defines the blending ratio for the middle layers of the models. This parameter controls the integration level of the models' middle layers.
    - Python dtype: `float`
- **`out`**
    - Comfy dtype: `FLOAT`
    - Determines the blending ratio for the output layer of the models. It affects the final output by adjusting the contribution of the second model's output layer.
    - Python dtype: `float`
## Output types
- **`model`**
    - Comfy dtype: `MODEL`
    - The resulting merged model, which is a hybrid of the two input models with patches applied according to the specified blending ratios.
    - Python dtype: `comfy.model_base.Model`
## Usage tips
- Infra type: `GPU`
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
