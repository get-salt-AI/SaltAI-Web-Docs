# ImpactConditionalBranchSelMode
## Documentation
- Class name: `ImpactConditionalBranchSelMode`
- Category: `ImpactPack/Logic`
- Output node: `False`

This node performs a conditional operation based on the input condition and selection mode. It evaluates the condition and, depending on the selection mode, either proceeds with the operation immediately or waits for execution to select the appropriate value.
## Input types
### Required
- **`cond`**
    - The condition to evaluate. It determines which value (true or false) to return based on its boolean state.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`sel_mode`**
    - The selection mode that decides when to select the true or false value. If true, selection is based on the prompt; if false, selection occurs at execution.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`tt_value`**
    - The value to return if the condition is true. This parameter is optional.
    - Python dtype: `typing.Union[None, typing.Any]`
    - Comfy dtype: `*`
- **`ff_value`**
    - The value to return if the condition is false. This parameter is optional.
    - Python dtype: `typing.Union[None, typing.Any]`
    - Comfy dtype: `*`
## Output types
- **`*`**
    - Returns the value based on the evaluated condition and selection mode.
    - Python dtype: `typing.Union[None, typing.Any]`
    - Comfy dtype: `*`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImpactConditionalBranchSelMode:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "cond": ("BOOLEAN",),
                "sel_mode": ("BOOLEAN", {"default": True, "label_on": "select_on_prompt", "label_off": "select_on_execution"}),
            },
            "optional": {
                "tt_value": (any_typ,),
                "ff_value": (any_typ,),
            },
        }

    FUNCTION = "doit"
    CATEGORY = "ImpactPack/Logic"

    RETURN_TYPES = (any_typ, )

    def doit(self, cond, sel_mode, tt_value=None, ff_value=None):
        print(f'tt={tt_value is None}\nff={ff_value is None}')
        if cond:
            return (tt_value,)
        else:
            return (ff_value,)

```
