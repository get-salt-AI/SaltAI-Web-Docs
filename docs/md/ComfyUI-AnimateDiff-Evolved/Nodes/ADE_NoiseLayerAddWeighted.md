# Noise Layer [Add Weighted] 🎭🅐🅓
## Documentation
- Class name: `ADE_NoiseLayerAddWeighted`
- Category: `Animate Diff 🎭🅐🅓/noise layers`
- Output node: `False`

This node specializes in adding a weighted noise layer to a given noise structure, utilizing a balance multiplier to fine-tune the influence of new noise versus existing noise. It extends the functionality of a basic noise addition by allowing for more nuanced control over how noise is integrated into the system.
## Input types
### Required
- **`batch_offset`**
    - Comfy dtype: `INT`
    - Specifies the offset for batch processing, affecting how noise is applied across different batches.
    - Python dtype: `int`
- **`noise_type`**
    - Comfy dtype: `COMBO[STRING]`
    - Defines the type of noise to be added, influencing the characteristics of the noise applied.
    - Python dtype: `str`
- **`seed_gen_override`**
    - Comfy dtype: `COMBO[STRING]`
    - Allows for overriding the default seed generation mechanism, enabling custom seed generation strategies.
    - Python dtype: `str`
- **`seed_offset`**
    - Comfy dtype: `INT`
    - Determines the offset applied to the seed value, providing a way to adjust the randomness of the noise.
    - Python dtype: `int`
- **`noise_weight`**
    - Comfy dtype: `FLOAT`
    - Controls the weight of the new noise being added, affecting the overall impact of the noise on the system.
    - Python dtype: `float`
- **`balance_multiplier`**
    - Comfy dtype: `FLOAT`
    - Adjusts the balance between new and existing noise, allowing for fine-tuned control over the noise layer's influence.
    - Python dtype: `float`
### Optional
- **`prev_noise_layers`**
    - Comfy dtype: `NOISE_LAYERS`
    - Optional previous noise layers that can be modified or extended with the new weighted noise layer.
    - Python dtype: `NoiseLayerGroup`
- **`mask_optional`**
    - Comfy dtype: `MASK`
    - An optional mask that can be applied to selectively add noise to certain areas.
    - Python dtype: `Tensor`
- **`seed_override`**
    - Comfy dtype: `INT`
    - Directly overrides the seed value used for noise generation, offering precise control over the noise's randomness.
    - Python dtype: `int`
## Output types
- **`noise_layers`**
    - Comfy dtype: `NOISE_LAYERS`
    - Returns the updated noise layer structure, including the newly added weighted noise layer.
    - Python dtype: `NoiseLayerGroup`
## Usage tips
- Infra type: `CPU`
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
