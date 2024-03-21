# TiledKSamplerProvider
## Documentation
- Class name: `TiledKSamplerProvider`
- Category: `ImpactPack/Sampler`
- Output node: `False`

This node provides a mechanism for sampling images using a tiled approach, which allows for the generation of high-resolution images by processing smaller tiles in parallel. It utilizes a variety of samplers and schedulers to control the sampling process, and supports different tiling strategies to optimize the sampling for specific use cases.
## Input types
### Required
- **`seed`**
    - A seed value to ensure reproducibility of the sampling process. It influences the randomness of the generated samples.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`steps`**
    - The number of steps to perform during the sampling process. It affects the quality and detail of the generated images.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`cfg`**
    - A configuration parameter that influences the sampling behavior, potentially affecting the quality of the generated images.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sampler_name`**
    - Specifies the sampler to use for the image generation process. Different samplers can produce varied effects.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`scheduler`**
    - Determines the scheduling strategy for the sampling process, affecting how samples are generated over time.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`denoise`**
    - A parameter to control the denoising level applied to the generated images, affecting their clarity and detail.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`tile_width`**
    - The width of the tiles used in the sampling process. It influences the granularity of the parallel processing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`tile_height`**
    - The height of the tiles used in the sampling process. It influences the granularity of the parallel processing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`tiling_strategy`**
    - The strategy used for tiling the image during the sampling process. Different strategies can affect the final image composition.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`basic_pipe`**
    - A basic pipeline configuration that includes the model and other essential components for the sampling process.
    - Python dtype: `tuple`
    - Comfy dtype: `BASIC_PIPE`
## Output types
- **`ksampler`**
    - The sampler configured and ready for generating images using the specified parameters.
    - Python dtype: `object`
    - Comfy dtype: `KSAMPLER`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class TiledKSamplerProvider:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                    "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    "steps": ("INT", {"default": 20, "min": 1, "max": 10000}),
                    "cfg": ("FLOAT", {"default": 8.0, "min": 0.0, "max": 100.0}),
                    "sampler_name": (comfy.samplers.KSampler.SAMPLERS, ),
                    "scheduler": (comfy.samplers.KSampler.SCHEDULERS, ),
                    "denoise": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 1.0, "step": 0.01}),
                    "tile_width": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tile_height": ("INT", {"default": 512, "min": 320, "max": MAX_RESOLUTION, "step": 64}),
                    "tiling_strategy": (["random", "padded", 'simple'], ),
                    "basic_pipe": ("BASIC_PIPE", )
                    }}

    RETURN_TYPES = ("KSAMPLER",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Sampler"

    def doit(self, seed, steps, cfg, sampler_name, scheduler, denoise,
             tile_width, tile_height, tiling_strategy, basic_pipe):
        model, _, _, positive, negative = basic_pipe
        sampler = core.TiledKSamplerWrapper(model, seed, steps, cfg, sampler_name, scheduler, positive, negative, denoise,
                                            tile_width, tile_height, tiling_strategy)
        return (sampler, )

```
