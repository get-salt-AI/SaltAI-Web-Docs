# Prepare Image For InsightFace
## Documentation
- Class name: `PrepImageForInsightFace`
- Category: `ipadapter`
- Output node: `False`

This node prepares an image for processing with InsightFace by optionally padding it, resizing it to a specific size, and applying sharpening. The process involves determining whether to pad the image and to what dimensions it should be resized, followed by the application of a specified sharpening level.
## Input types
### Required
- **`image`**
    - The input image to be processed. It is the primary subject for preparation before being processed by InsightFace.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`crop_position`**
    - Specifies the position from which the image should be cropped. This affects the focus area of the image before further processing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`sharpening`**
    - The level of sharpening to apply to the image. This can enhance the image's details for better facial recognition.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`pad_around`**
    - A boolean indicating whether padding should be added around the image. This can affect the image's aspect ratio and size.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`image`**
    - The processed image, ready for InsightFace analysis. It has been optionally padded, resized, and sharpened according to the specified parameters.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApplyFaceID`

The PrepImageForInsightFace node is essential for optimizing images for facial feature enhancement with the InsightFace model, by adjusting parameters like padding, resizing, and sharpening based on the input image's characteristics. It is particularly useful in pipelines focusing on style and character face adaptation, where precise facial feature analysis is crucial.
## Source code
```python
class PrepImageForInsightFace:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "image": ("IMAGE",),
            "crop_position": (["center", "top", "bottom", "left", "right"],),
            "sharpening": ("FLOAT", {"default": 0.0, "min": 0, "max": 1, "step": 0.05}),
            "pad_around": ("BOOLEAN", { "default": True }),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "prep_image"

    CATEGORY = "ipadapter"

    def prep_image(self, image, crop_position, sharpening=0.0, pad_around=True):
        if pad_around:
            padding = 30
            size = (580, 580)
        else:
            padding = 0
            size = (640, 640)
        output = prepImage(image, "LANCZOS", crop_position, size, sharpening, padding)

        return (output, )

```
