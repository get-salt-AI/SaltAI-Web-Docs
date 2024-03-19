# StableCascade_StageB_Conditioning
## Documentation
- Class name: `StableCascade_StageB_Conditioning`
- Category: `_for_testing/stable_cascade`
- Output node: `False`

The `set_prior` method updates the conditioning data by incorporating a 'stable_cascade_prior' derived from the 'stage_c' latent representation into each element of the conditioning data. This process enriches the conditioning context with information from a later stage in the cascade, potentially enhancing the generation quality in subsequent steps.
## Input types
### Required
- **`conditioning`**
    - The conditioning data to be updated. It is crucial for guiding the generation process by providing context and constraints.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`stage_c`**
    - The latent representation from a later stage in the cascade. It is used to enrich the conditioning data, providing additional context for the generation process.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Output types
- **`conditioning`**
    - The updated conditioning data, now enriched with 'stable_cascade_prior' information from the 'stage_c' latent representation.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class StableCascade_StageB_Conditioning:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "conditioning": ("CONDITIONING",),
                              "stage_c": ("LATENT",),
                             }}
    RETURN_TYPES = ("CONDITIONING",)

    FUNCTION = "set_prior"

    CATEGORY = "_for_testing/stable_cascade"

    def set_prior(self, conditioning, stage_c):
        c = []
        for t in conditioning:
            d = t[1].copy()
            d['stable_cascade_prior'] = stage_c['samples']
            n = [t[0], d]
            c.append(n)
        return (c, )

```
