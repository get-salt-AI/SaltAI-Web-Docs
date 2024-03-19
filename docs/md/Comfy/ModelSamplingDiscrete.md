# ModelSamplingDiscrete
## Documentation
- Class name: `ModelSamplingDiscrete`
- Category: `advanced/model`
- Output node: `False`

The `ModelSamplingDiscrete` node is designed to modify a given model by applying a discrete sampling strategy. It supports various sampling methods such as `eps`, `v_prediction`, `lcm`, and `x0`, each of which alters the model's behavior in specific ways. This node also allows for the optional use of zero-shot noise rescaling (zsnr) to adjust the model's sigma values for noise control.
## Input types
### Required
- **`model`**
    - The `model` parameter represents the model to be modified. It is the core input around which the sampling strategy is applied, determining the base behavior before any modifications.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`sampling`**
    - The `sampling` parameter specifies the discrete sampling strategy to be applied to the model. It affects how the model generates outputs by altering its internal sampling mechanism.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`zsnr`**
    - The `zsnr` flag, when enabled, applies zero-shot noise rescaling to the model's sigma values. This can help in controlling the noise level in the model's output, potentially improving the quality.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`model`**
    - The modified model with the applied discrete sampling strategy and optional zero-shot noise rescaling.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `Reroute,KSampler,SamplerCustom,CR Module Pipe Loader,FreeU (Advanced),Anything Everywhere,ReroutePrimitive|pysssss,BasicScheduler,KSampler (Efficient)`


## Source code
```python
class ModelSamplingDiscrete:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "sampling": (["eps", "v_prediction", "lcm", "x0"],),
                              "zsnr": ("BOOLEAN", {"default": False}),
                              }}

    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "advanced/model"

    def patch(self, model, sampling, zsnr):
        m = model.clone()

        sampling_base = comfy.model_sampling.ModelSamplingDiscrete
        if sampling == "eps":
            sampling_type = comfy.model_sampling.EPS
        elif sampling == "v_prediction":
            sampling_type = comfy.model_sampling.V_PREDICTION
        elif sampling == "lcm":
            sampling_type = LCM
            sampling_base = ModelSamplingDiscreteDistilled
        elif sampling == "x0":
            sampling_type = X0

        class ModelSamplingAdvanced(sampling_base, sampling_type):
            pass

        model_sampling = ModelSamplingAdvanced(model.model.model_config)
        if zsnr:
            model_sampling.set_sigmas(rescale_zero_terminal_snr_sigmas(model_sampling.sigmas))

        m.add_object_patch("model_sampling", model_sampling)
        return (m, )

```
