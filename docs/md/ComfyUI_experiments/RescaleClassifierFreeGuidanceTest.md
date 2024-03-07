# RescaleClassifierFreeGuidanceTest
## Documentation
- Class name: `RescaleClassifierFreeGuidanceTest`
- Category: `custom_node_experiments`
- Output node: `False`

This method modifies a given model by integrating a custom sampler configuration function. This function recalibrates the classifier-free guidance (CFG) signal, blending the original and modified CFG signals based on a specified multiplier. The adjustment is aimed at refining the model's output, offering a tunable parameter to control the influence of CFG on the generation process.
## Input types
- **`model`**
    - The model to be modified by applying a custom sampler configuration function that rescales the classifier-free guidance (CFG) signal based on a multiplier.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`multiplier`**
    - A scalar that adjusts the strength of the rescaled CFG signal, blending between the original and rescaled CFG signals. It's a tunable parameter that influences the final output of the model.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified model with an integrated custom sampler configuration function for recalibrating the CFG signal. This output is crucial for applying the adjusted CFG signal in the model's generation process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used to refine the output of AI models by adjusting the strength of the classifier-free guidance signal, making it essential for applications requiring precise control over AI-generated content. It takes a `MODEL` and a `multiplier` as input, and outputs a modified `MODEL` with an integrated custom sampler configuration function for recalibrating the CFG signal.
## Source code
```python
class RescaleClassifierFreeGuidance:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "multiplier": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "custom_node_experiments"

    def patch(self, model, multiplier):
        
        def rescale_cfg(args):
            cond = args["cond"]
            uncond = args["uncond"]
            cond_scale = args["cond_scale"]

            x_cfg = uncond + cond_scale * (cond - uncond)
            ro_pos = torch.std(cond, dim=(1,2,3), keepdim=True)
            ro_cfg = torch.std(x_cfg, dim=(1,2,3), keepdim=True)

            x_rescaled = x_cfg * (ro_pos / ro_cfg)
            x_final = multiplier * x_rescaled + (1.0 - multiplier) * x_cfg

            return x_final

        m = model.clone()
        m.set_model_sampler_cfg_function(rescale_cfg)
        return (m, )

```
