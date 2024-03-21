# RescaleCFG
## Documentation
- Class name: `RescaleCFG`
- Category: `advanced/model`
- Output node: `False`

The `RescaleCFG` node applies a rescaling operation to the conditional guidance scale (CFG) of a given model. This operation adjusts the influence of the conditional guidance on the model's output, aiming to balance between the conditioned and unconditioned generation. The rescaling is controlled by a multiplier, allowing for fine-tuning of the model's behavior.
## Input types
### Required
- **`model`**
    - The model to which the CFG rescaling will be applied. This operation clones the model and modifies its sampler configuration to incorporate the rescaling logic.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`multiplier`**
    - A scaling factor that determines the degree of rescaling applied to the CFG. It allows for adjusting the balance between conditioned and unconditioned generation, with values closer to 1 increasing the influence of the conditional guidance.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified model with the CFG rescaling applied. This model is a clone of the input model, with its sampler configuration adjusted to incorporate the rescaling logic.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class RescaleCFG:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "multiplier": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "advanced/model"

    def patch(self, model, multiplier):
        def rescale_cfg(args):
            cond = args["cond"]
            uncond = args["uncond"]
            cond_scale = args["cond_scale"]
            sigma = args["sigma"]
            sigma = sigma.view(sigma.shape[:1] + (1,) * (cond.ndim - 1))
            x_orig = args["input"]

            #rescale cfg has to be done on v-pred model output
            x = x_orig / (sigma * sigma + 1.0)
            cond = ((x - (x_orig - cond)) * (sigma ** 2 + 1.0) ** 0.5) / (sigma)
            uncond = ((x - (x_orig - uncond)) * (sigma ** 2 + 1.0) ** 0.5) / (sigma)

            #rescalecfg
            x_cfg = uncond + cond_scale * (cond - uncond)
            ro_pos = torch.std(cond, dim=(1,2,3), keepdim=True)
            ro_cfg = torch.std(x_cfg, dim=(1,2,3), keepdim=True)

            x_rescaled = x_cfg * (ro_pos / ro_cfg)
            x_final = multiplier * x_rescaled + (1.0 - multiplier) * x_cfg

            return x_orig - (x - x_final * sigma / (sigma * sigma + 1.0) ** 0.5)

        m = model.clone()
        m.set_model_sampler_cfg_function(rescale_cfg)
        return (m, )

```
