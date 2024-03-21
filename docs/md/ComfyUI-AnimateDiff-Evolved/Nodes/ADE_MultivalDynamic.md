# Multival Dynamic 🎭🅐🅓
## Documentation
- Class name: `ADE_MultivalDynamic`
- Category: `Animate Diff 🎭🅐🅓/multival`
- Output node: `False`

The `ADE_MultivalDynamic` node dynamically generates multivalued outputs based on the provided mask and float range inputs. It supports scaling the mask values either absolutely or relatively to fit within the specified float range, allowing for flexible adjustment of the mask's intensity or effect.
## Input types
### Required
- **`float_val`**
    - Specifies the float value or range to be applied directly or used for scaling the mask. It can be a single float or a list of floats, providing flexibility in adjusting the mask's intensity or effect.
    - Python dtype: `Union[float, list[float]]`
    - Comfy dtype: `FLOAT`
### Optional
- **`mask_optional`**
    - The optional mask tensor to be scaled or directly modified based on the float_val input. It represents the area or effect to be dynamically adjusted.
    - Python dtype: `Optional[torch.Tensor]`
    - Comfy dtype: `MASK`
## Output types
- **`multival`**
    - Generates a multivalued output based on the scaled mask or direct float value application, providing dynamic control over the mask's intensity or effect.
    - Python dtype: `MultivalDynamicNode`
    - Comfy dtype: `MULTIVAL`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ADE_ApplyAnimateDiffModel`


## Source code
```python
class MultivalDynamicNode:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "float_val": ("FLOAT", {"default": 1.0, "min": 0.0, "step": 0.001},),
            },
            "optional": {
                "mask_optional": ("MASK",)
            }
        }
    
    RETURN_TYPES = ("MULTIVAL",)
    CATEGORY = "Animate Diff 🎭🅐🅓/multival"
    FUNCTION = "create_multival"

    def create_multival(self, float_val: Union[float, list[float]]=1.0, mask_optional: Tensor=None):
        # first, normalize inputs
        # if float_val is iterable, treat as a list and assume inputs are floats
        float_is_iterable = False
        if isinstance(float_val, Iterable):
            float_is_iterable = True
            float_val = list(float_val)
            # if mask present, make sure float_val list can be applied to list - match lengths
            if mask_optional is not None:
                if len(float_val) < mask_optional.shape[0]:
                    # copies last entry enough times to match mask shape
                    float_val = float_val + float_val[-1]*(mask_optional.shape[0]-len(float_val))
                float_val = float_val[:mask_optional.shape[0]]
        # now that inputs are normalized, figure out what value to actually return
        if mask_optional is not None:
            mask_optional = mask_optional.clone()
            mask_optional = mask_optional * float_val
            return (mask_optional,)
        else:
            if not float_is_iterable:
                return (float_val,)
            # create a dummy mask of b,h,w=float_len,1,1 (sigle pixel)
            # purpose is for float input to work with mask code, without special cases
            float_len = len(float_val) if float_is_iterable else 1
            shape = (float_len,1,1)
            mask_optional = torch.ones(shape)
            mask_optional = mask_optional * float_val
            return (mask_optional,)

```
