# Batch Float (mtb)
## Documentation
- Class name: `Batch Float (mtb)`
- Category: `mtb/batch`
- Output node: `False`

The BatchFloat node is designed to manage and manipulate batches of floating-point numbers, enabling operations such as creation, transformation, and aggregation of float batches for further processing or analysis.
## Input types
### Required
- **`mode`**
    - Specifies the interpolation mode for generating float batches, either as a single value or in steps, influencing the pattern of the generated batch.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `Union[str, List[str]]`
- **`count`**
    - Determines the number of float values to generate, directly affecting the size of the output batch.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`min`**
    - Sets the minimum value limit for the generated float values, influencing the lower bound of the batch's value range.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`max`**
    - Defines the maximum value limit for the generated float values, setting the upper bound of the batch's value range.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`easing`**
    - Selects the easing function to apply during float value generation, affecting the distribution and transition of values within the batch.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `Union[str, List[str]]`
## Output types
- **`floats`**
    - Comfy dtype: `FLOATS`
    - A list of floating-point numbers assembled or generated by the node, representing the processed batch.
    - Python dtype: `List[float]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BatchFloat:
    """Generates a batch of float values with interpolation"""

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "mode": (
                    ["Single", "Steps"],
                    {"default": "Steps"},
                ),
                "count": ("INT", {"default": 1}),
                "min": ("FLOAT", {"default": 0.0}),
                "max": ("FLOAT", {"default": 1.0}),
                "easing": (
                    [
                        "Linear",
                        "Sine In",
                        "Sine Out",
                        "Sine In/Out",
                        "Quart In",
                        "Quart Out",
                        "Quart In/Out",
                        "Cubic In",
                        "Cubic Out",
                        "Cubic In/Out",
                        "Circ In",
                        "Circ Out",
                        "Circ In/Out",
                        "Back In",
                        "Back Out",
                        "Back In/Out",
                        "Elastic In",
                        "Elastic Out",
                        "Elastic In/Out",
                        "Bounce In",
                        "Bounce Out",
                        "Bounce In/Out",
                    ],
                    {"default": "Linear"},
                ),
            }
        }

    FUNCTION = "set_floats"
    RETURN_TYPES = ("FLOATS",)
    CATEGORY = "mtb/batch"

    def set_floats(self, mode, count, min, max, easing):
        keyframes = []
        if mode == "Single":
            keyframes = [min] * count
            return (keyframes,)

        for i in range(count):
            normalized_step = i / (count - 1)
            eased_step = apply_easing(normalized_step, easing)
            eased_value = min + (max - min) * eased_step
            keyframes.append(eased_value)

        return (keyframes,)

```
