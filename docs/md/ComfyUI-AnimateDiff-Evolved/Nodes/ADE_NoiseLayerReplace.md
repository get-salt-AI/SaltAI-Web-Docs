# Noise Layer [Replace] 🎭🅐🅓
## Documentation
- Class name: `ADE_NoiseLayerReplace`
- Category: `Animate Diff 🎭🅐🅓/noise layers`
- Output node: `False`

This node is responsible for creating and managing a sequence of noise layers where each layer replaces the previous noise with a new one based on a specified mask. It allows for dynamic modification of noise in a batch of images or signals, facilitating various effects or transformations.
## Input types
### Required
- **`batch_offset`**
    - Specifies the offset for the batch processing, allowing for staggered or phased processing across batches.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`noise_type`**
    - Defines the type of noise to be applied. This can vary the effect and application of the noise layer, influencing the final output.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed_gen_override`**
    - Allows for overriding the default seed generation mechanism, enabling custom or specific seed generation strategies for noise creation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`seed_offset`**
    - Provides an additional offset to the seed value, allowing for fine-tuned control over the noise generation process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
### Optional
- **`prev_noise_layers`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `NOISE_LAYERS`
- **`mask_optional`**
    - An optional mask tensor that can be used to control where the noise is applied, enabling selective noise application.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
- **`seed_override`**
    - Optionally overrides the seed used for noise generation, allowing for deterministic noise patterns.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`noise_layers`**
    - Returns the updated sequence of noise layers after adding the new replacement layer, facilitating the layering and management of noise effects.
    - Python dtype: `NoiseLayerGroup`
    - Comfy dtype: `NOISE_LAYERS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class NoiseLayerReplaceNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "batch_offset": ("INT", {"default": 0, "min": 0, "max": BIGMAX}),
                "noise_type": (NoiseLayerType.LIST,),
                "seed_gen_override": (SeedNoiseGeneration.LIST_WITH_OVERRIDE,),
                "seed_offset": ("INT", {"default": 0, "min": BIGMIN, "max": BIGMAX}),
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
                      prev_noise_layers: NoiseLayerGroup=None, mask_optional: Tensor=None, seed_override: int=None,):
        # prepare prev_noise_layers
        if prev_noise_layers is None:
            prev_noise_layers = NoiseLayerGroup()
        prev_noise_layers = prev_noise_layers.clone()
        # create layer
        layer = NoiseLayerReplace(noise_type=noise_type, batch_offset=batch_offset, seed_gen_override=seed_gen_override, seed_offset=seed_offset,
                                  seed_override=seed_override, mask=mask_optional)
        prev_noise_layers.add_to_start(layer)
        return (prev_noise_layers,)

```
