# VideoLinearCFGGuidance
## Documentation
- Class name: `VideoLinearCFGGuidance`
- Category: `sampling/video_models`
- Output node: `False`

This node applies a linear conditioning guidance scale to a video model. It modifies the model's sampler configuration function to adjust the influence of conditioned and unconditioned inputs based on a linear scale between a minimum configuration scale and the provided condition scale.
## Input types
### Required
- **`model`**
    - The video model to which the linear CFG guidance will be applied. This model is cloned and modified.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`min_cfg`**
    - The minimum conditioning scale used as the starting point of the linear scale applied to the model's conditioning guidance.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified video model with the linear CFG guidance applied.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,FreeU_V2,KSamplerAdvanced`


## Source code
```python
class VideoLinearCFGGuidance:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "min_cfg": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step":0.5, "round": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "sampling/video_models"

    def patch(self, model, min_cfg):
        def linear_cfg(args):
            cond = args["cond"]
            uncond = args["uncond"]
            cond_scale = args["cond_scale"]

            scale = torch.linspace(min_cfg, cond_scale, cond.shape[0], device=cond.device).reshape((cond.shape[0], 1, 1, 1))
            return uncond + scale * (cond - uncond)

        m = model.clone()
        m.set_model_sampler_cfg_function(linear_cfg)
        return (m, )

```
