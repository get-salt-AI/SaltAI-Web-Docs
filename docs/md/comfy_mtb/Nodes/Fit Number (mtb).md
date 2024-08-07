# Fit Number (mtb)
## Documentation
- Class name: `Fit Number (mtb)`
- Category: `mtb/math`
- Output node: `False`

The Fit Number node is designed to adjust a single numerical value to fit within a specified target range. It supports clamping to ensure the value stays within the target range and allows for the application of easing functions to modify the distribution of the transformed value.
## Input types
### Required
- **`value`**
    - A single numerical value to be transformed. The node adjusts this value to fit within the specified target range, potentially altering its distribution based on the easing function applied.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`clamp`**
    - A boolean indicating whether to clamp the transformed value to the target range, ensuring it does not fall outside the specified limits.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`source_min`**
    - The minimum value of the source range. It is used as the lower bound when transforming the value.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`source_max`**
    - The maximum value of the source range. It serves as the upper bound for the transformation of the value.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`target_min`**
    - The minimum value of the target range. This is the lower limit to which the input value is scaled.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`target_max`**
    - The maximum value of the target range. This defines the upper limit to which the input value is adjusted.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`easing`**
    - A string representing the easing function applied to the distribution of the transformed value, affecting how it is scaled between the source and target ranges.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`float`**
    - Comfy dtype: `FLOAT`
    - The input value transformed to fit within the specified target range, potentially altered in distribution by the easing function.
    - Python dtype: `float`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class MTB_FitNumber:
    """Fit the input float using a source and target range"""

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "value": ("FLOAT", {"default": 0, "forceInput": True}),
                "clamp": ("BOOLEAN", {"default": False}),
                "source_min": (
                    "FLOAT",
                    {"default": 0.0, "step": 0.01, "min": -1e5},
                ),
                "source_max": (
                    "FLOAT",
                    {"default": 1.0, "step": 0.01, "min": -1e5},
                ),
                "target_min": (
                    "FLOAT",
                    {"default": 0.0, "step": 0.01, "min": -1e5},
                ),
                "target_max": (
                    "FLOAT",
                    {"default": 1.0, "step": 0.01, "min": -1e5},
                ),
                "easing": (
                    EASINGS,
                    {"default": "Linear"},
                ),
            }
        }

    FUNCTION = "set_range"
    RETURN_TYPES = ("FLOAT",)
    CATEGORY = "mtb/math"
    DESCRIPTION = "Fit the input float using a source and target range"

    def set_range(
        self,
        value: float,
        clamp: bool,
        source_min: float,
        source_max: float,
        target_min: float,
        target_max: float,
        easing: str,
    ):
        if source_min == source_max:
            normalized_value = 0
        else:
            normalized_value = (value - source_min) / (source_max - source_min)
        if clamp:
            normalized_value = max(min(normalized_value, 1), 0)

        eased_value = apply_easing(normalized_value, easing)

        # - Convert the eased value to the target range
        res = target_min + (target_max - target_min) * eased_value

        return (res,)

```
