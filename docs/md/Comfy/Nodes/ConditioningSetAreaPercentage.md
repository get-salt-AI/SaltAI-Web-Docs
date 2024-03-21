# Conditioning (Set Area with Percentage)
## Documentation
- Class name: `ConditioningSetAreaPercentage`
- Category: `conditioning`
- Output node: `False`

This node modifies the conditioning by setting the area of interest as a percentage of the total image size. It allows for specifying the area's width, height, and its position (x, y) relative to the image, along with the strength of the conditioning effect. This is useful for focusing the model's attention on specific parts of an image during generation.
## Input types
### Required
- **`conditioning`**
    - The conditioning data to be modified. It's crucial for directing the model's focus or altering its behavior based on specified conditions.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`width`**
    - The width of the area of interest as a percentage of the total image width. It defines how wide the focused area should be.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`height`**
    - The height of the area of interest as a percentage of the total image height. It defines how tall the focused area should be.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`x`**
    - The x-coordinate of the top-left corner of the area of interest, as a percentage of the total image width. It specifies where the area begins horizontally.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`y`**
    - The y-coordinate of the top-left corner of the area of interest, as a percentage of the total image height. It specifies where the area begins vertically.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`strength`**
    - The strength of the conditioning effect within the specified area. It determines how strongly the model should focus on or alter this area.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The modified conditioning data with the specified area and strength settings applied. It's used to direct the model's focus or alter its behavior.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ConditioningSetAreaPercentage:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"conditioning": ("CONDITIONING", ),
                              "width": ("FLOAT", {"default": 1.0, "min": 0, "max": 1.0, "step": 0.01}),
                              "height": ("FLOAT", {"default": 1.0, "min": 0, "max": 1.0, "step": 0.01}),
                              "x": ("FLOAT", {"default": 0, "min": 0, "max": 1.0, "step": 0.01}),
                              "y": ("FLOAT", {"default": 0, "min": 0, "max": 1.0, "step": 0.01}),
                              "strength": ("FLOAT", {"default": 1.0, "min": 0.0, "max": 10.0, "step": 0.01}),
                             }}
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "append"

    CATEGORY = "conditioning"

    def append(self, conditioning, width, height, x, y, strength):
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['area'] = ("percentage", height, width, y, x)
            n[1]['strength'] = strength
            n[1]['set_area_to_bounds'] = False
            c.append(n)
        return (c, )

```
