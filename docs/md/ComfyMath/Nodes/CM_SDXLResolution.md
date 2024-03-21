# SDXLResolution
## Documentation
- Class name: `CM_SDXLResolution`
- Category: `math/graphics`
- Output node: `False`

This node takes a resolution string as input and returns the width and height as separate integers. It is designed to work with specific resolutions supported by the SDXL framework, parsing the input string to extract the numerical values.
## Input types
### Required
- **`resolution`**
    - Specifies the desired resolution in a 'width x height' format string. It must match one of the predefined resolutions supported by the SDXL framework, affecting the output width and height directly.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`int`**
    - The height component of the specified resolution, extracted from the input string.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Usage tips
- Infra type: `CPU`
- Common nodes: `Eff. Loader SDXL,intToFloat _O`

The CM_SDXLResolution node is primarily utilized for setting the target resolution for image processing tasks within the pipeline, by taking a resolution string as input and outputting the corresponding width and height as integers. This node ensures that AI models and custom nodes operate on images at consistent and optimal resolutions, as defined by the user or the pipeline's requirements.
## Source code
```python
class SDXLResolution:
    @classmethod
    def INPUT_TYPES(cls) -> Mapping[str, Any]:
        return {
            "required": {
                "resolution": (
                    [f"{res[0]}x{res[1]}" for res in SDXL_SUPPORTED_RESOLUTIONS],
                )
            }
        }

    RETURN_TYPES = ("INT", "INT")
    RETURN_NAMES = ("width", "height")
    FUNCTION = "op"
    CATEGORY = "math/graphics"

    def op(self, resolution: str) -> tuple[int, int]:
        width, height = resolution.split("x")
        return (int(width), int(height))

```
