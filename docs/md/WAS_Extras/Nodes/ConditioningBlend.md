# Conditioning (Blend)
## Documentation
- Class name: `ConditioningBlend`
- Category: `conditioning`
- Output node: `False`

The ConditioningBlend node blends two conditioning inputs using specified blending modes and strength, potentially influenced by a seed for deterministic results. It supports operations like spherical linear interpolation (SLERP) and subtraction, allowing for flexible manipulation of conditioning data.
## Input types
### Required
- **`conditioning_a`**
    - The first conditioning input to blend. It plays a crucial role in determining the base of the blend operation.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
- **`conditioning_b`**
    - The second conditioning input to blend. It contributes to the blend operation by providing an additional set of conditioning data to mix with the first input.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
- **`blending_mode`**
    - Specifies the method used for blending the two conditioning inputs. Different modes can produce varied effects on the resulting conditioning.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`blending_strength`**
    - Determines the intensity of the blend between the two conditioning inputs. A higher value results in a stronger influence of the second input.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`seed`**
    - An optional seed for random number generation, ensuring reproducible results when blending.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`conditioning`**
    - The result of blending the two conditioning inputs according to the specified mode and strength.
    - Python dtype: `List[Tuple[torch.Tensor, Dict[str, torch.Tensor]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

The ConditioningBlend node is utilized for merging two sets of conditioning data, such as those from different AI models or inputs, by applying a specified blending mode and strength, optionally influenced by a seed for consistent outcomes. It is particularly useful for adjusting the influence of one set of conditioning data over another, resulting in a customized conditioning output that can be used for more nuanced AI model interactions.
## Source code
```python
class WAS_ConditioningBlend:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "conditioning_a": ("CONDITIONING", ),
                "conditioning_b": ("CONDITIONING", ),
                "blending_mode": (list(blending_modes.keys()), ),
                "blending_strength": ("FLOAT", {"default": 0.5, "min": -10.0, "max": 10.0, "step": 0.001}),
                "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
            }
        }

    RETURN_TYPES = ("CONDITIONING",)
    RETURN_NAMES = ("conditioning",)
    FUNCTION = "combine"

    CATEGORY = "conditioning"

    def combine(self, conditioning_a, conditioning_b, blending_mode, blending_strength, seed):
    
        if seed > 0:
            torch.manual_seed(seed)
    
        a = conditioning_a[0][0].clone()
        b = conditioning_b[0][0].clone()
        
        pa = conditioning_a[0][1]["pooled_output"].clone()
        pb = conditioning_b[0][1]["pooled_output"].clone()

        cond = normalize(blending_modes[blending_mode](a, b, 1 - blending_strength))
        pooled = normalize(blending_modes[blending_mode](pa, pb, 1 - blending_strength))
        
        conditioning = [[cond, {"pooled_output": pooled}]]

        return (conditioning, )

```
