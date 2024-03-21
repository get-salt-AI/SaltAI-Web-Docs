# Sample Settings 🎭🅐🅓
## Documentation
- Class name: `ADE_AnimateDiffSamplingSettings`
- Category: `Animate Diff 🎭🅐🅓`
- Output node: `False`

This node is responsible for generating sample settings for the AnimateDiff process. It allows customization of various parameters such as batch offset, noise type, seed generation method, and more, which collectively define how the sampling operation will be conducted.
## Input types
### Required
- **`batch_offset`**
    - Specifies the offset for the batch in the sampling process, allowing for control over the starting point of the batch.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_type`**
    - Determines the type of noise to be applied during the sampling process. This affects the texture and quality of the generated samples.
    - Python dtype: `str`
    - Comfy dtype: `['default', 'constant', 'empty'...]`
- **`seed_gen`**
    - Defines the method for generating seeds used in the noise generation process, impacting the randomness and variation of the samples.
    - Python dtype: `str`
    - Comfy dtype: `['comfy', 'auto1111'...]`
- **`seed_offset`**
    - Sets the offset for the seed generation, enabling fine-tuning of the seed values used in noise generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`noise_layers`**
    - Specifies the configuration of noise layers to be applied, influencing the depth and complexity of the noise effect in the samples.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
- **`iteration_opts`**
    - Defines iteration-specific options, offering further customization of the sampling process.
    - Python dtype: `IterationOptions`
    - Comfy dtype: `ITERATION_OPTS`
- **`seed_override`**
    - Allows for the manual setting of a specific seed value, overriding the automatic seed generation mechanism.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`adapt_denoise_steps`**
    - Enables or disables the adaptation of denoising steps based on the sampling settings, affecting the clarity and detail of the samples.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`sample_settings`**
    - The generated sample settings, encapsulating all specified parameters and configurations for the sampling process.
    - Python dtype: `SampleSettings`
    - Comfy dtype: `SAMPLE_SETTINGS`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SampleSettingsNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "noise_type": (NoiseLayerType.LIST,),
                "seed_gen": (SeedNoiseGeneration.LIST,),
                "seed_offset": ("INT", {"default": 0, "min": BIGMIN, "max": BIGMAX}),
            },
            "optional": {
                "noise_layers": ("NOISE_LAYERS",),
                "iteration_opts": ("ITERATION_OPTS",),
                "seed_override": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff, "forceInput": True}),
                "adapt_denoise_steps": ("BOOLEAN", {"default": False},),
            }
        }
    
    RETURN_TYPES = ("SAMPLE_SETTINGS",)
    RETURN_NAMES = ("settings",)
    CATEGORY = "Animate Diff 🎭🅐🅓"
    FUNCTION = "create_settings"

    def create_settings(self, batch_offset: int, noise_type: str, seed_gen: str, seed_offset: int, noise_layers: NoiseLayerGroup=None,
                        iteration_opts: IterationOptions=None, seed_override: int=None, adapt_denoise_steps=False):
        sampling_settings = SampleSettings(batch_offset=batch_offset, noise_type=noise_type, seed_gen=seed_gen, seed_offset=seed_offset, noise_layers=noise_layers,
                                           iteration_opts=iteration_opts, seed_override=seed_override, adapt_denoise_steps=adapt_denoise_steps)
        return (sampling_settings,)

```
