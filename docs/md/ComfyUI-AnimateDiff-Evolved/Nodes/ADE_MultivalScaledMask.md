# Multival Scaled Mask 🎭🅐🅓
## Documentation
- Class name: `ADE_MultivalScaledMask`
- Category: `Animate Diff 🎭🅐🅓/multival`
- Output node: `False`

The ADE_MultivalScaledMask node is designed to dynamically adjust the scale of masks based on provided minimum and maximum float values, potentially applying different scaling strategies. It is part of the Animate Diff Evolved suite, focusing on manipulating mask values for animation and differential effects. This node allows for precise control over the scaling of mask values, enabling users to define how mask intensities should be adjusted within specified bounds, and supports both absolute and relative scaling methods.
## Input types
### Required
- **`min_float_val`**
    - Specifies the minimum float value for scaling the mask. This parameter sets the lower bound for the mask's scale adjustment.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`max_float_val`**
    - Defines the maximum float value for scaling the mask. It sets the upper limit for how much the mask can be scaled.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`mask`**
    - The mask to be scaled. This parameter is the target mask whose values will be adjusted based on the scaling parameters.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
### Optional
- **`scaling`**
    - Determines the scaling strategy to be applied to the mask. It can be either absolute or relative, affecting how the mask values are adjusted within the specified min and max float values.
    - Python dtype: `str`
    - Comfy dtype: `['absolute', 'relative']`
## Output types
- **`multival`**
    - The output represents the scaled mask values, encapsulated within a structure that supports multiple values, adhering to the Animate Diff Evolved suite's requirements for handling multivalued data.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MULTIVAL`
- **`ui`**
    - The output is a modified version of the input mask, scaled according to the specified minimum and maximum float values and the chosen scaling strategy.
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MultivalScaledMaskNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "min_float_val": ("FLOAT", {"default": 0.0, "min": 0.0, "step": 0.001}),
                "max_float_val": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001}),
                "mask": ("MASK",),
            },
            "optional": {
                "scaling": (ScaleType.LIST,),
            }
        }

    RETURN_TYPES = ("MULTIVAL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/multival"
    FUNCTION = "create_multival"

    def create_multival(self, min_float_val: float, max_float_val: float, mask: Tensor, scaling: str=ScaleType.ABSOLUTE):
        if isinstance(min_float_val, Iterable):
            raise ValueError(f"min_float_val must be type float (no lists allowed here), not {type(min_float_val).__name__}.")
        if isinstance(max_float_val, Iterable):
            raise ValueError(f"max_float_val must be type float (no lists allowed here), not {type(max_float_val).__name__}.")
        
        if scaling == ScaleType.ABSOLUTE:
            mask = linear_conversion(mask.clone(), new_min=min_float_val, new_max=max_float_val)
        elif scaling == ScaleType.RELATIVE:
            mask = normalize_min_max(mask.clone(), new_min=min_float_val, new_max=max_float_val)
        else:
            raise ValueError(f"scaling '{scaling}' not recognized.")
        return MultivalDynamicNode.create_multival(self, mask_optional=mask)

```
