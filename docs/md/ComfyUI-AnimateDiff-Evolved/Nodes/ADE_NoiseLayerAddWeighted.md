# Noise Layer [Add Weighted] 🎭🅐🅓
## Documentation
- Class name: `ADE_NoiseLayerAddWeighted`
- Category: `Animate Diff 🎭🅐🅓/noise layers`
- Output node: `False`

This node creates a weighted noise layer that combines new and old noise based on a calculated noise mask, adjusting the blend with a balance multiplier. It's designed to add complexity and variation to the noise pattern by weighting the contribution of new noise against the existing noise in a dynamic manner.
## Input types
### Required
- **`batch_offset`**
    - Specifies the offset for batch processing, affecting how noise is applied across different items in a batch.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_type`**
    - Determines the type of noise to be applied, influencing the texture and characteristics of the resulting noise.
    - Python dtype: `str`
    - Comfy dtype: `['default', 'constant', 'empty', 'repeated_context', 'FreeNoise']`
- **`seed_gen_override`**
    - Overrides the default seed generation mechanism, allowing for customized noise generation patterns.
    - Python dtype: `str`
    - Comfy dtype: `['comfy', 'auto1111', 'seed_gen_override_option1', 'seed_gen_override_option2']`
- **`seed_offset`**
    - Adjusts the seed value, enabling fine control over the randomness of the noise generation.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_weight`**
    - Controls the intensity of the new noise being added, allowing for adjustments in the noise's impact.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`balance_multiplier`**
    - Modifies the balance between the old and new noise, enabling nuanced adjustments to the final noise effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`prev_noise_layers`**
    - Specifies the previous group of noise layers to which the new layer will be added, allowing for the accumulation of noise effects.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
- **`mask_optional`**
    - Applies a mask to selectively enable or disable noise application on specific areas, enhancing the flexibility of noise addition.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`seed_override`**
    - Directly sets the seed value, providing precise control over the noise generation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`noise_layers`**
    - Returns the updated group of noise layers, including the newly added weighted noise layer.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class NoiseLayerAddWeightedNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "noise_type": (NoiseLayerType.LIST,),
                "seed_gen_override": (SeedNoiseGeneration.LIST_WITH_OVERRIDE,),
                "seed_offset": ("INT", {"default": 0, "min": BIGMIN, "max": BIGMAX}),
                "noise_weight": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 10.0, "step": 0.001}),
                "balance_multiplier": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
            },
            "optional": {
                "prev_noise_layers": ("NOISE_LAYERS",),
                "mask_optional": ("MASK",),
                "seed_override": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff, "forceInput": True}),
            }
        }
    
    RETURN_TYPES = ("NOISE_LAYERS",)
    CATEGORY = "Animate Diff 🎭🅐🅓/noise layers"
    FUNCTION = "create_layers"

    def create_layers(self, batch_offset: int, noise_type: str, seed_gen_override: str, seed_offset: int,
                      noise_weight: float, balance_multiplier: float,
                      prev_noise_layers: NoiseLayerGroup=None, mask_optional: Tensor=None, seed_override: int=None,):
        # prepare prev_noise_layers
        if prev_noise_layers is None:
            prev_noise_layers = NoiseLayerGroup()
        prev_noise_layers = prev_noise_layers.clone()
        # create layer
        layer = NoiseLayerAddWeighted(noise_type=noise_type, batch_offset=batch_offset, seed_gen_override=seed_gen_override, seed_offset=seed_offset,
                              seed_override=seed_override, mask=mask_optional,
                              noise_weight=noise_weight, balance_multiplier=balance_multiplier)
        prev_noise_layers.add_to_start(layer)
        return (prev_noise_layers,)

```
