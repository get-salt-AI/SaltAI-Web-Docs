# FreeU_V2
## Documentation
- Class name: `FreeU_V2`
- Category: `model_patches`
- Output node: `False`

The `FreeU_V2` node applies a patch to a model, modifying its output block to adjust the scale of certain layers based on predefined scale factors and applying a Fourier filter to the hidden state patch. This process is designed to enhance the model's output by manipulating the scale and frequency components of the hidden states.
## Input types
### Required
- **`model`**
    - The model to which the patch will be applied. This patch modifies the model's behavior by adjusting the scale of its layers and applying Fourier filtering to enhance the output.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`b1`**
    - A scale factor for adjusting the model's layers. It's part of the mechanism to enhance the model's output by scaling certain layers differently.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`b2`**
    - Another scale factor for layer adjustment, working alongside `b1` to fine-tune the model's output enhancement.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s1`**
    - A scale parameter for the Fourier filter applied to the hidden state patch, influencing the frequency components manipulation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s2`**
    - Another scale parameter for the Fourier filter, working with `s1` to adjust the frequency components of the model's output.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified model with the applied patch, featuring adjusted layer scales and Fourier-filtered hidden states for enhanced output.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,UltimateSDUpscale,FaceDetailer,Anything Everywhere,Reroute,PatchModelAddDownscale,KSamplerAdvanced,VideoLinearCFGGuidance,IPAdapter,UltimateSDUpscaleNoUpscale`


## Source code
```python
class FreeU_V2:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                             "b1": ("FLOAT", {"default": 1.3, "min": 0.0, "max": 10.0, "step": 0.01}),
                             "b2": ("FLOAT", {"default": 1.4, "min": 0.0, "max": 10.0, "step": 0.01}),
                             "s1": ("FLOAT", {"default": 0.9, "min": 0.0, "max": 10.0, "step": 0.01}),
                             "s2": ("FLOAT", {"default": 0.2, "min": 0.0, "max": 10.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "model_patches"

    def patch(self, model, b1, b2, s1, s2):
        model_channels = model.model.model_config.unet_config["model_channels"]
        scale_dict = {model_channels * 4: (b1, s1), model_channels * 2: (b2, s2)}
        on_cpu_devices = {}

        def output_block_patch(h, hsp, transformer_options):
            scale = scale_dict.get(h.shape[1], None)
            if scale is not None:
                hidden_mean = h.mean(1).unsqueeze(1)
                B = hidden_mean.shape[0]
                hidden_max, _ = torch.max(hidden_mean.view(B, -1), dim=-1, keepdim=True)
                hidden_min, _ = torch.min(hidden_mean.view(B, -1), dim=-1, keepdim=True)
                hidden_mean = (hidden_mean - hidden_min.unsqueeze(2).unsqueeze(3)) / (hidden_max - hidden_min).unsqueeze(2).unsqueeze(3)

                h[:,:h.shape[1] // 2] = h[:,:h.shape[1] // 2] * ((scale[0] - 1 ) * hidden_mean + 1)

                if hsp.device not in on_cpu_devices:
                    try:
                        hsp = Fourier_filter(hsp, threshold=1, scale=scale[1])
                    except:
                        print("Device", hsp.device, "does not support the torch.fft functions used in the FreeU node, switching to CPU.")
                        on_cpu_devices[hsp.device] = True
                        hsp = Fourier_filter(hsp.cpu(), threshold=1, scale=scale[1]).to(hsp.device)
                else:
                    hsp = Fourier_filter(hsp.cpu(), threshold=1, scale=scale[1]).to(hsp.device)

            return h, hsp

        m = model.clone()
        m.set_model_output_block_patch(output_block_patch)
        return (m, )

```
