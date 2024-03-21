# Add CLIP SDXL Params
## Documentation
- Class name: `BNK_AddCLIPSDXLParams`
- Category: `conditioning/advanced`
- Output node: `False`

The `BNK_AddCLIPSDXLParams` node is designed to augment the conditioning data with additional parameters related to image dimensions and cropping. This process is essential for tailoring the conditioning to specific requirements of image generation tasks, ensuring that the generated images match the desired dimensions and cropping specifications.
## Input types
### Required
- **`conditioning`**
    - Represents the initial conditioning data that will be augmented with additional parameters. It is crucial for the generation process, serving as the base upon which further specifications are added.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
- **`width`**
    - Specifies the desired width of the generated image. This parameter directly influences the dimensions of the output, ensuring the generated image meets the specified width.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`height`**
    - Determines the height of the generated image, directly affecting its dimensions to match the specified height.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_w`**
    - Defines the width of the cropping area, allowing for precise control over the portion of the image to be included or excluded.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_h`**
    - Specifies the height of the cropping area, enabling fine-tuning of the image's visible section according to the desired cropping.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`target_width`**
    - Sets the target width for the image after processing, ensuring the final image aligns with the specified dimensions.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`target_height`**
    - Indicates the target height for the image post-processing, guaranteeing the output matches the desired height.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`conditioning`**
    - The augmented conditioning data, now including the specified image dimensions and cropping parameters, ready for use in further image generation processes.
    - Python dtype: `List[Tuple[Any, Dict[str, Any]]]`
    - Comfy dtype: `CONDITIONING`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class AddCLIPSDXLParams:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "conditioning": ("CONDITIONING", ),
            "width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "crop_w": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION}),
            "crop_h": ("INT", {"default": 0, "min": 0, "max": MAX_RESOLUTION}),
            "target_width": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            "target_height": ("INT", {"default": 1024.0, "min": 0, "max": MAX_RESOLUTION}),
            }}
    
    RETURN_TYPES = ("CONDITIONING",)
    FUNCTION = "encode"

    CATEGORY = "conditioning/advanced"

    def encode(self, conditioning, width, height, crop_w, crop_h, target_width, target_height):
        c = []
        for t in conditioning:
            n = [t[0], t[1].copy()]
            n[1]['width'] = width
            n[1]['height'] = height
            n[1]['crop_w'] = crop_w
            n[1]['crop_h'] = crop_h
            n[1]['target_width'] = target_width
            n[1]['target_height'] = target_height
            c.append(n)
        return (c,)

```
