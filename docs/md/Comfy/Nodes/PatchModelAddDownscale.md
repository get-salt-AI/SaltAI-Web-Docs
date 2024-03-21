# PatchModelAddDownscale (Kohya Deep Shrink)
## Documentation
- Class name: `PatchModelAddDownscale`
- Category: `_for_testing`
- Output node: `False`

This node applies a downscaling and optional upscaling transformation to specific blocks within a model's architecture, based on sigma values derived from start and end percentages. It allows for the adjustment of the model's internal representations at different stages of processing, potentially enhancing detail or altering the model's behavior in generating outputs.
## Input types
### Required
- **`model`**
    - The model to be patched with downscaling and optional upscaling transformations. It serves as the base for modifications aimed at altering the model's internal processing for potentially enhanced output generation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`block_number`**
    - Specifies the block within the model's architecture where the downscaling transformation is to be applied. This allows for targeted modification of the model's processing at a specific stage.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`downscale_factor`**
    - Determines the factor by which the input is downscaled. A higher downscale factor results in a more significant reduction in dimensionality, potentially affecting the model's detail capturing capability.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`start_percent`**
    - Defines the starting point of the sigma range for applying the downscaling transformation. This parameter helps in targeting the transformation to specific stages of the model's processing based on noise levels.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_percent`**
    - Sets the end point of the sigma range for the downscaling transformation, allowing for precise control over the stages of the model's processing that are affected.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`downscale_after_skip`**
    - A boolean flag indicating whether the downscaling should be applied after a skip connection within the model. This choice can influence how the model integrates information across different processing stages.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`downscale_method`**
    - Specifies the method used for downscaling the input. Different methods can affect the quality and characteristics of the downscaled output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`upscale_method`**
    - Determines the method used for optional upscaling after downscaling. Choosing an appropriate method can influence the restoration of detail in the upscaled output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - The patched model with downscaling and optional upscaling transformations applied to specified blocks. This modified model can exhibit altered behavior in generating outputs, potentially with enhanced detail or different characteristics.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,KRestartSamplerAdv,ModelSamplingDiscrete,LoraLoader,IPAdapterApply`


## Source code
```python
class PatchModelAddDownscale:
    upscale_methods = ["bicubic", "nearest-exact", "bilinear", "area", "bislerp"]
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "block_number": ("INT", {"default": 3, "min": 1, "max": 32, "step": 1}),
                              "downscale_factor": ("FLOAT", {"default": 2.0, "min": 0.1, "max": 9.0, "step": 0.001}),
                              "start_percent": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001}),
                              "end_percent": ("FLOAT", {"default": 0.35, "min": 0.0, "max": 1.0, "step": 0.001}),
                              "downscale_after_skip": ("BOOLEAN", {"default": True}),
                              "downscale_method": (s.upscale_methods,),
                              "upscale_method": (s.upscale_methods,),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "_for_testing"

    def patch(self, model, block_number, downscale_factor, start_percent, end_percent, downscale_after_skip, downscale_method, upscale_method):
        sigma_start = model.model.model_sampling.percent_to_sigma(start_percent)
        sigma_end = model.model.model_sampling.percent_to_sigma(end_percent)

        def input_block_patch(h, transformer_options):
            if transformer_options["block"][1] == block_number:
                sigma = transformer_options["sigmas"][0].item()
                if sigma <= sigma_start and sigma >= sigma_end:
                    h = comfy.utils.common_upscale(h, round(h.shape[-1] * (1.0 / downscale_factor)), round(h.shape[-2] * (1.0 / downscale_factor)), downscale_method, "disabled")
            return h

        def output_block_patch(h, hsp, transformer_options):
            if h.shape[2] != hsp.shape[2]:
                h = comfy.utils.common_upscale(h, hsp.shape[-1], hsp.shape[-2], upscale_method, "disabled")
            return h, hsp

        m = model.clone()
        if downscale_after_skip:
            m.set_model_input_block_patch_after_skip(input_block_patch)
        else:
            m.set_model_input_block_patch(input_block_patch)
        m.set_model_output_block_patch(output_block_patch)
        return (m, )

```
