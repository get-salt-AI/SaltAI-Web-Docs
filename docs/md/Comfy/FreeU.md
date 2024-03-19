# FreeU
## Documentation
- Class name: `FreeU`
- Category: `model_patches`
- Output node: `False`

The `FreeU` node is designed to modify the output of a model by applying a custom patching function. This function adjusts the model's output based on predefined scaling factors and optionally applies a Fourier filter to handle spatial frequencies. The patching process is intended to enhance or alter the model's output in a specific manner, potentially improving the quality or relevance of the generated content.
## Input types
### Required
- **`model`**
    - The model to be patched. This is the primary input and the target of the patching operation, which will have its output block modified to apply the custom adjustments.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`b1`**
    - A scaling factor for adjusting the model's output. It is part of the scale dictionary that determines how the output is modified.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`b2`**
    - Another scaling factor for adjusting the model's output, used in conjunction with `b1` to define the scale dictionary.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s1`**
    - A scaling parameter for the Fourier filter, affecting how spatial frequencies are handled in the patching process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`s2`**
    - Another scaling parameter for the Fourier filter, working alongside `s1` to fine-tune the spatial frequency handling.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The patched model, which has been modified by the `FreeU` node to include custom adjustments and potentially enhanced output.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KSamplerAdvanced,UltimateSDUpscale,Bus Node,DynamicThresholdingFull,Anything Everywhere`


## Source code
```python
class FreeU:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                             "b1": ("FLOAT", {"default": 1.1, "min": 0.0, "max": 10.0, "step": 0.01}),
                             "b2": ("FLOAT", {"default": 1.2, "min": 0.0, "max": 10.0, "step": 0.01}),
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
                h[:,:h.shape[1] // 2] = h[:,:h.shape[1] // 2] * scale[0]
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
