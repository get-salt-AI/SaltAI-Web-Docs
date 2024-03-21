# TomePatchModel
## Documentation
- Class name: `TomePatchModel`
- Category: `_for_testing`
- Output node: `False`

The `TomePatchModel` node applies a patch to a given model, modifying its attention mechanism with a custom function to potentially enhance its performance. This patching process involves cloning the original model and applying modifications to the attention mechanism based on the provided ratio and the original shape of the input, aiming to improve the model's handling of input data.
## Input types
### Required
- **`model`**
    - The model to be patched. This parameter is crucial as it determines the base model that will undergo the patching process to potentially enhance its performance.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
- **`ratio`**
    - A floating-point value that influences the patching process by adjusting the degree of modification applied to the model's attention mechanism. It plays a significant role in tailoring the patch to achieve desired enhancements.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The patched model, which has undergone modifications to its attention mechanism based on the provided ratio and the original shape of the input. This output is ready for further use or evaluation.
    - Python dtype: `ModelPatcher`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class TomePatchModel:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "ratio": ("FLOAT", {"default": 0.3, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "_for_testing"

    def patch(self, model, ratio):
        self.u = None
        def tomesd_m(q, k, v, extra_options):
            #NOTE: In the reference code get_functions takes x (input of the transformer block) as the argument instead of q
            #however from my basic testing it seems that using q instead gives better results
            m, self.u = get_functions(q, ratio, extra_options["original_shape"])
            return m(q), k, v
        def tomesd_u(n, extra_options):
            return self.u(n)

        m = model.clone()
        m.set_model_attn1_patch(tomesd_m)
        m.set_model_attn1_output_patch(tomesd_u)
        return (m, )

```
