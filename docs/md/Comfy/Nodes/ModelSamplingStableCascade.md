# ModelSamplingStableCascade
## Documentation
- Class name: `ModelSamplingStableCascade`
- Category: `advanced/model`
- Output node: `False`

This node applies a patch to a given model to enhance its sampling capabilities by integrating a custom sampling strategy. It clones the input model and adds a new sampling method, which is a combination of StableCascadeSampling and EPS, configured with a specified shift parameter. This allows for advanced control over the sampling process, potentially improving the model's performance or generating results with specific characteristics.
## Input types
### Required
- **`model`**
    - The model to which the sampling strategy will be applied. This parameter is crucial as it determines the base model that will be enhanced with the new sampling method.
    - Python dtype: `comfy.model_sampling.Model`
    - Comfy dtype: `MODEL`
- **`shift`**
    - A floating-point value that specifies the shift parameter for the new sampling method. This parameter fine-tunes the sampling process, affecting the characteristics of the generated results.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The enhanced model with the new sampling method applied. This model is a clone of the input model, augmented with the custom sampling strategy.
    - Python dtype: `comfy.model_sampling.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class ModelSamplingStableCascade:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "shift": ("FLOAT", {"default": 2.0, "min": 0.0, "max": 100.0, "step":0.01}),
                              }}

    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "advanced/model"

    def patch(self, model, shift):
        m = model.clone()

        sampling_base = comfy.model_sampling.StableCascadeSampling
        sampling_type = comfy.model_sampling.EPS

        class ModelSamplingAdvanced(sampling_base, sampling_type):
            pass

        model_sampling = ModelSamplingAdvanced(model.model.model_config)
        model_sampling.set_parameters(shift)
        m.add_object_patch("model_sampling", model_sampling)
        return (m, )

```
