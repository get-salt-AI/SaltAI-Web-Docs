# ModelSamplerTonemapNoiseTest
## Documentation
- Class name: `ModelSamplerTonemapNoiseTest`
- Category: `custom_node_experiments`
- Output node: `False`

This node applies a custom patch to a given model that modifies its sampling behavior. Specifically, it introduces a tonemap noise test by adjusting the magnitude of noise predictions based on the Reinhard tonemapping operator. This adjustment is scaled by a user-defined multiplier, allowing for control over the intensity of the effect. The patched model is then returned, enabling its use in subsequent sampling processes.
## Input types
- **`model`**
    - The model to which the custom patch will be applied. This patch modifies the model's sampling behavior to include a tonemap noise test.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`multiplier`**
    - A scaling factor that controls the intensity of the tonemap noise effect applied by the patch. It is an optional parameter with a default value of 1.0.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The model after applying the tonemap noise patch. This model incorporates the specified tonemap and noise adjustments, enabling enhanced sampling capabilities.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

Often used with model sampling processes to apply a custom patch that introduces a tonemap noise test, adjusting the noise predictions based on the Reinhard tonemapping operator with a user-defined multiplier for controlling the intensity. This node takes a `MODEL` as input and outputs a patched `MODEL` with enhanced sampling capabilities.
## Source code
```python
class ModelSamplerTonemapNoiseTest:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 100.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "custom_node_experiments"

    def patch(self, model, multiplier):
        
        def sampler_tonemap_reinhard(args):
            cond = args["cond"]
            uncond = args["uncond"]
            cond_scale = args["cond_scale"]
            noise_pred = (cond - uncond)
            noise_pred_vector_magnitude = (torch.linalg.vector_norm(noise_pred, dim=(1)) + 0.0000000001)[:,None]
            noise_pred /= noise_pred_vector_magnitude

            mean = torch.mean(noise_pred_vector_magnitude, dim=(1,2,3), keepdim=True)
            std = torch.std(noise_pred_vector_magnitude, dim=(1,2,3), keepdim=True)

            top = (std * 3 + mean) * multiplier

            #reinhard
            noise_pred_vector_magnitude *= (1.0 / top)
            new_magnitude = noise_pred_vector_magnitude / (noise_pred_vector_magnitude + 1.0)
            new_magnitude *= top

            return uncond + noise_pred * new_magnitude * cond_scale

        m = model.clone()
        m.set_model_sampler_cfg_function(sampler_tonemap_reinhard)
        return (m, )

```
