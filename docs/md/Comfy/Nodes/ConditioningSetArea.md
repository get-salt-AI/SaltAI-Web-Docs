# Conditioning (Set Area)
## Documentation
- Class name: `ConditioningSetArea`
- Category: `conditioning`
- Output node: `False`

This node modifies the conditioning data by setting specific areas and strengths for each conditioning element. It's used to apply localized adjustments to the conditioning, allowing for more precise control over the generation process.
## Input types
### Required
- **`conditioning`**
    - The base conditioning data to be modified. It's crucial for defining the context or content that the generation process should adhere to.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`width`**
    - Specifies the width of the area to be set. It determines how wide the area of effect will be.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Specifies the height of the area to be set. It determines how tall the area of effect will be.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`x`**
    - The x-coordinate of the top-left corner of the area to be set. It positions the area horizontally.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`y`**
    - The y-coordinate of the top-left corner of the area to be set. It positions the area vertically.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`strength`**
    - Determines the intensity of the effect applied to the specified area. Higher values result in stronger effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The modified conditioning data with specified areas and strengths applied to each element.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `ConditioningCombine,SetNode`


## Source code
```python
class ConditioningSetArea:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                              "width": ("INT", {"default": 64, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                              "height": ("INT", {"default": 64, "min": 64, "max": MAX_RESOLUTION, "step": 8}),
                              "x": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "y": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION, "step": 8}),
                              "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "append"

    CATEGORY = "conditioning"

    def append(self, conditioning, width, height, x, y, strength):
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['area'] = (height // 8, width // 8, y // 8, x // 8)
            n[1]['strength'] = strength
            n[1]['set_area_to_bounds'] = False
            c.append(n)
        return (c, )

```
