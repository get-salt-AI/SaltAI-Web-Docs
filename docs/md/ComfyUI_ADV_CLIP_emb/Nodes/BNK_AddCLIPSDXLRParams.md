# Add CLIP SDXL Refiner Params
## Documentation
- Class name: `BNK_AddCLIPSDXLRParams`
- Category: `conditioning/advanced`
- Output node: `False`

The `BNK_AddCLIPSDXLRParams` node enriches the conditioning data with additional parameters such as width, height, and an aesthetic score. This process is essential for tailoring the conditioning to specific requirements, thereby enhancing the generation process.
## Input types
### Required
- **`conditioning`**
    - The base conditioning data to be enriched with additional parameters. It is crucial for defining the context or requirements for the generation process.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`width`**
    - Specifies the desired width for the generation. It directly influences the dimensions of the generated output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Specifies the desired height for the generation. It directly influences the dimensions of the generated output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`ascore`**
    - An aesthetic score that influences the visual appeal of the generated output. It allows for a subjective quality assessment to be factored into the generation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`conditioning`**
    - The enriched conditioning data, now including width, height, and aesthetic score, ready for further processing.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AddCLIPSDXLRParams:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "conditioning": ("CONDITIONING", ),
            "width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "ascore": ("FLOAT", {"default": 6.0, "min": 0.0, "max": 1000.0, "step": 0.01}),
            }}
    
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "conditioning/advanced"

    def encode(self, conditioning, width, height, ascore):
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['width'] = width
            n[1]['height'] = height
            n[1]['aesthetic_score'] = ascore
            c.append(n)
        return (c,)

```
