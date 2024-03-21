# ModelSamplingContinuousEDM
## Documentation
- Class name: `ModelSamplingContinuousEDM`
- Category: `advanced/model`
- Output node: `False`

This node patches a given model with an advanced continuous EDM (Energy-based Diffusion Model) sampling strategy, allowing for the specification of a range for the noise levels (sigma) used during the sampling process. It supports different types of sampling methods, such as `eps` and `v_prediction`, to adjust the model's behavior for generating outputs.
## Input types
### Required
- **`model`**
    - The model to be patched with the advanced continuous EDM sampling strategy. It is the core component that will be modified to incorporate the new sampling behavior.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
- **`sampling`**
    - Specifies the type of sampling method to be used, such as `eps` for epsilon sampling or `v_prediction` for velocity prediction. This choice influences how the model generates outputs by altering the noise application strategy.
    - Python dtype: `Union[str, List[str]]`
    - Comfy dtype: `['STRING']`
- **`sigma_max`**
    - The maximum value of noise level (sigma) allowed in the sampling process. It defines the upper bound of the noise range, influencing the variability and quality of the generated outputs.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sigma_min`**
    - The minimum value of noise level (sigma) allowed in the sampling process. It sets the lower bound of the noise range, affecting the smoothness and detail of the generated outputs.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The patched model with the advanced continuous EDM sampling strategy applied. This modified model is capable of generating outputs with the specified noise level range and sampling method.
    - Python dtype: `comfy.model_management.Model`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `FreeU_V2`


## Source code
```python
class ModelSamplingContinuousEDM:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "sampling": (["v_prediction", "eps"],),
                              "sigma_max": ("FLOAT", {"default": 120.0, "min": 0.0, "max": 1000.0, "step":0.001, "round": False}),
                              "sigma_min": ("FLOAT", {"default": 0.002, "min": 0.0, "max": 1000.0, "step":0.001, "round": False}),
                              }}

    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "advanced/model"

    def patch(self, model, sampling, sigma_max, sigma_min):
        m = model.clone()

        if sampling == "eps":
            sampling_type = comfy.model_sampling.EPS
        elif sampling == "v_prediction":
            sampling_type = comfy.model_sampling.V_PREDICTION

        class ModelSamplingAdvanced(comfy.model_sampling.ModelSamplingContinuousEDM, sampling_type):
            pass

        model_sampling = ModelSamplingAdvanced(model.model.model_config)
        model_sampling.set_sigma_range(sigma_min, sigma_max)
        m.add_object_patch("model_sampling", model_sampling)
        return (m, )

```
