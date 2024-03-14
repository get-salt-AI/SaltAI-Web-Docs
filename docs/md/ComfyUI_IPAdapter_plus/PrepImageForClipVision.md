# Prepare Image For Clip Vision
## Documentation
- Class name: `PrepImageForClipVision`
- Category: `ipadapter`
- Output node: `False`

This node prepares an image for processing by CLIP vision models by resizing, cropping, and optionally sharpening the image. It standardizes the image format to meet the input requirements of CLIP models, ensuring compatibility and optimal performance.
## Input types
### Required
- **`image`**
    - The input image to be processed. This image will be resized, cropped, and optionally sharpened according to the specified parameters.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`interpolation`**
    - Specifies the interpolation method to be used when resizing the image. Different methods can affect the quality and characteristics of the resized image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`crop_position`**
    - Determines the position of the crop applied to the image, which can influence the focus area of the processed image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`sharpening`**
    - Applies a sharpening filter to the image, enhancing edge definition and detail. The strength of the sharpening effect can be adjusted.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The processed image, ready for input into CLIP vision models.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IDGenerationNode,IPAdapterApply,AIO_Preprocessor,ImageBatch,CLIPVisionEncode,PrepImageForInsightFace,SetNode,IPAdapterApplyFaceID,PreviewImage,IPAdapterEncoder`

The PrepImageForClipVision node is essential for preparing images for compatibility with CLIP vision models by resizing, cropping, and optionally sharpening the image to meet the input requirements of CLIP models. It is often used with IPAdapters and CLIPVisionLoader to ensure that the processed images are optimized for analysis or further processing, enhancing the model's ability to accurately interpret and generate images based on textual descriptions or visual inputs.
## Source code
```python
class PrepImageForClipVision:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
            "image": ("IMAGE",),
            "interpolation": (["LANCZOS", "BICUBIC", "HAMMING", "BILINEAR", "BOX", "NEAREST"],),
            "crop_position": (["top", "bottom", "left", "right", "center", "pad"],),
            "sharpening": ("FLOAT", {"default": 0.0, "min": 0, "max": 1, "step": 0.05}),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "prep_image"

    CATEGORY = "ipadapter"

    def prep_image(self, image, interpolation="LANCZOS", crop_position="center", sharpening=0.0):
        size = (224, 224)
        output = prepImage(image, interpolation, crop_position, size, sharpening, 0)
        return (output, )

```
