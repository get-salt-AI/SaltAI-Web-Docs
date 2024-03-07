# TonemapNoiseWithRescaleCFG
## Documentation
- Class name: `TonemapNoiseWithRescaleCFG`
- Category: `custom_node_experiments`
- Output node: `False`

This node applies a combination of tonemapping and noise rescaling techniques to modify the behavior of a given model's sampling process. It first calculates the magnitude of the difference between conditional and unconditional predictions, applies a tonemapping operation using the Reinhard method to normalize these magnitudes, and then rescales the conditional guidance scale factor (CFG) based on the standard deviation of the original and modified predictions. This process aims to adjust the model's output in a way that balances detail preservation and noise reduction, potentially enhancing the quality of generated samples.
## Input types
- **`model`**
    - The model to be modified. This node clones the provided model and applies the tonemap and rescale operations to its sampling process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`tonemap_multiplier`**
    - A multiplier that adjusts the intensity of the tonemapping operation. Higher values increase the effect of tonemapping on the noise prediction magnitudes.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`rescale_multiplier`**
    - A multiplier that adjusts the intensity of the rescaling operation applied to the CFG. It determines the balance between the original and modified predictions in the final output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified model with the tonemap and rescale operations applied to its sampling process. This model is expected to produce enhanced samples with a balance between detail preservation and noise reduction.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: `Pack SDXL Tuple`

The TonemapNoiseWithRescaleCFG node is designed to enhance the quality of AI-generated samples by applying tonemapping and noise rescaling techniques to adjust the model's sampling process. It is particularly useful for models that require a balance between detail preservation and noise reduction, taking a model as input and outputting a modified version of the model with adjusted conditional guidance scale factor (CFG).
## Source code
```python
class TonemapNoiseWithRescaleCFG:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"model": ("MODEL",),
                             "tonemap_multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step": 0.01}),
                             "rescale_multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                             }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "custom_node_experiments"

    def patch(self, model, tonemap_multiplier, rescale_multiplier):

        def tonemap_noise_rescale_cfg(args):
            cond = args["cond"]
            uncond = args["uncond"]
            cond_scale = args["cond_scale"]

            # Tonemap
            noise_pred = (cond - uncond)
            noise_pred_vector_magnitude = (torch.linalg.vector_norm(noise_pred, dim=(1)) + 0.0000000001)[:, None]
            noise_pred /= noise_pred_vector_magnitude

            mean = torch.mean(noise_pred_vector_magnitude, dim=(1, 2, 3), keepdim=True)
            std = torch.std(noise_pred_vector_magnitude, dim=(1, 2, 3), keepdim=True)

            top = (std * 3 + mean) * tonemap_multiplier

            # Reinhard
            noise_pred_vector_magnitude *= (1.0 / top)
            new_magnitude = noise_pred_vector_magnitude / (noise_pred_vector_magnitude + 1.0)
            new_magnitude *= top

            # Rescale CFG
            x_cfg = uncond + (noise_pred * new_magnitude * cond_scale)
            ro_pos = torch.std(cond, dim=(1, 2, 3), keepdim=True)
            ro_cfg = torch.std(x_cfg, dim=(1, 2, 3), keepdim=True)

            x_rescaled = x_cfg * (ro_pos / ro_cfg)
            x_final = rescale_multiplier * x_rescaled + (1.0 - rescale_multiplier) * x_cfg

            return x_final

        m = model.clone()
        m.set_model_sampler_cfg_function(tonemap_noise_rescale_cfg)
        return (m, )

```
