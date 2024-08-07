---
tags:
- Color
- HSVColorSpace
---

# BuildColorRangeAdvanced (hsv)
## Documentation
- Class name: `BuildColorRangeAdvanced (hsv)`
- Category: `Bmad/CV/Color A.`
- Output node: `False`

The `BuildColorRangeAdvanced (hsv)` node is designed to create a sophisticated range of colors in the HSV color space. It leverages advanced techniques to adjust and fix the bounds of hue, saturation, and value based on given parameters, enabling precise color selection and manipulation for various computer vision tasks.
## Input types
### Required
- **`samples`**
    - Specifies the HSV color samples from which to derive the color range. It is essential for determining the base colors for range calculation.
    - Comfy dtype: `HSV_SAMPLES`
    - Python dtype: `HSV_Samples`
- **`hue_exp`**
    - Defines the expression or method to adjust the hue component of the color range. It allows for dynamic hue range adjustments based on specific criteria or algorithms.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`sat_exp`**
    - Specifies the expression or method to adjust the saturation component of the color range. It enables fine-tuning of the saturation levels within the generated color range.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`val_exp`**
    - Indicates the expression or method to adjust the value (brightness) component of the color range. It facilitates the manipulation of brightness levels to achieve the desired color range.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`hsv_color`**
    - Comfy dtype: `HSV_COLOR`
    - Returns the calculated HSV color range based on the provided expressions and samples. It represents the final color range after all adjustments have been applied.
    - Python dtype: `Tuple[int, int, int]`
- **`combo[string]`**
    - Comfy dtype: `COMBO[STRING]`
    - Provides additional information or results related to the color range calculation, potentially including details about the applied adjustments or methods.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BuildColorRangeHSVAdvanced:
    def __init__(self):
        self.samples = None

    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
            # "average": ("HSV_COLOR",), # compute from sample?
            "samples": ("HSV_SAMPLES",),
            "hue_exp": ("STRING", {"multiline": True, "default": cls.default_hue_expression}),
            "sat_exp": ("STRING", {"multiline": True, "default": cls.default_saturation_expression}),
            "val_exp": ("STRING", {"multiline": True, "default": cls.default_value_expression}),
        }}

    RETURN_TYPES = ("HSV_COLOR", "HSV_COLOR", InRangeHSV.hue_modes)
    FUNCTION = "get_interval"
    CATEGORY = f"{cv_category_path}/Color A."

    default_hue_expression = """# hue
h_quant2(0, 1).scale_by_constant(16) if 2 < v_median < 253 else to_interval(0, 180)
    """
    default_saturation_expression = """# saturation
to_interval(5, 255) if 2 < v_median < 253 else s_quant2(0,1).interpolate(0.2, [0, 255])
    """
    default_value_expression = """# value
v_quant2(0,1).interpolate(.5, [0, 255]).scale_by_constant(50) if 2 < v_median < 253 else v_quant2(0,1).scale_by_constant(8)
    """

    def get_interval(self, samples, hue_exp, sat_exp, val_exp):
        self.samples = samples

        # function to get sample names to include (avoids pre computing everything)
        # this supposes some computations could take considerable time, thus avoiding them if not used
        def valid_token(token: str):
            return token in samples_names and (
                    token.startswith("h_") or token.startswith("s_") or token.startswith("v_") or
                    token == "to_interval" or token == "minmax" or token == "maxmin"
            )

        # get bounds from the eval expressions
        bounds = {}
        samples_names = dir(samples)
        for key, expression in {"h": hue_exp, "s": sat_exp, "v": val_exp}.items():
            expression = prepare_text_for_eval(expression)  # purge potentially dangerous tokens

            locals_to_include_names = filter_expression_names(valid_token, expression)
            locals_to_include = {
                name: getattr(samples, name)
                for name in locals_to_include_names
            }

            bounds[key] = eval(expression, {
                "__builtins__": {},
                'min': min, 'max': max, 'm': math,
                **locals_to_include
            }, {})

        # get 2 colors that represent the computed lower and upper bounds
        lower_bounds = np.array([bounds.get("h")[0], bounds.get("s")[0], bounds.get("v")[0]]).round()
        upper_bounds = np.array([bounds.get("h")[1], bounds.get("s")[1], bounds.get("v")[1]]).round()
        hue_mode = BuildColorRangeHSV.fix_bounds(lower_bounds, upper_bounds)
        return (upper_bounds, lower_bounds, hue_mode)

```
