# Noise Layer [Add] 🎭🅐🅓
## Documentation
- Class name: `ADE_NoiseLayerAdd`
- Category: `Animate Diff 🎭🅐🅓/noise layers`
- Output node: `False`

The `ADE_NoiseLayerAdd` node is designed to add a new noise layer to a sequence of existing noise layers, specifically by adding the new noise to the old noise with an optional weight. This process is part of the AnimateDiff framework, which is used for generating or modifying images through differential noise application. The node allows for the customization of noise application, enabling more nuanced control over the noise generation process.
## Input types
### Required
- **`batch_offset`**
    - Specifies the offset for the batch processing. This parameter is crucial for managing the sequence in which noise layers are applied, ensuring that the noise addition is correctly aligned with the batch processing order.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_type`**
    - Defines the type of noise to be added. This parameter is essential for determining the characteristics of the noise being applied, affecting the visual outcome of the noise addition.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed_gen_override`**
    - Allows for the override of the default seed generation method. This is important for ensuring consistency in noise generation across different iterations or for introducing variability.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed_offset`**
    - Determines the offset applied to the seed value. This parameter is used to modify the seed for noise generation, enabling fine-tuning of the noise characteristics.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_weight`**
    - Specifies the weight of the new noise being added. This parameter controls the intensity of the noise addition, allowing for the adjustment of the noise's impact on the final image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`prev_noise_layers`**
    - Represents the existing sequence of noise layers to which the new noise layer will be added. This parameter is crucial for maintaining the continuity and order of noise application.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
- **`mask_optional`**
    - An optional mask that can be applied to the noise addition. This allows for selective application of noise, enabling more precise control over the areas affected by the noise.
    - Python dtype: `Tensor`
    - Comfy dtype: `TENSOR`
- **`seed_override`**
    - Provides an option to override the default seed value. This parameter allows for direct control over the seed used for noise generation, offering a way to customize the noise characteristics.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`noise_layers`**
    - Returns the updated sequence of noise layers, including the newly added noise layer. This output is essential for subsequent noise layer applications or for final noise generation.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class NoiseLayerAddNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "noise_type": (NoiseLayerType.LIST,),
                "seed_gen_override": (SeedNoiseGeneration.LIST_WITH_OVERRIDE,),
                "seed_offset": ("INT", {"default": 0, "min": BIGMIN, "max": BIGMAX}),
                "noise_weight": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 10.0, "step": 0.001}),
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
                      noise_weight: float,
                      prev_noise_layers: NoiseLayerGroup=None, mask_optional: Tensor=None, seed_override: int=None,):
        # prepare prev_noise_layers
        if prev_noise_layers is None:
            prev_noise_layers = NoiseLayerGroup()
        prev_noise_layers = prev_noise_layers.clone()
        # create layer
        layer = NoiseLayerAdd(noise_type=noise_type, batch_offset=batch_offset, seed_gen_override=seed_gen_override, seed_offset=seed_offset,
                              seed_override=seed_override, mask=mask_optional,
                              noise_weight=noise_weight)
        prev_noise_layers.add_to_start(layer)
        return (prev_noise_layers,)

```
