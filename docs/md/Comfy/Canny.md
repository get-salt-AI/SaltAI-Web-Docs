# Canny
## Documentation
- Class name: `Canny`
- Category: `image/preprocessors`
- Output node: `False`

The `Canny` node applies the Canny edge detection algorithm to an input image, utilizing thresholds to identify and filter edges. This process involves converting the image to grayscale, applying a Gaussian blur, computing gradients, and performing non-maximum suppression and hysteresis to accurately identify edges.
## Input types
### Required
- **`image`**
    - The input image to which the Canny edge detection algorithm will be applied. It's crucial for identifying and filtering edges in the image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`low_threshold`**
    - The lower threshold for the hysteresis procedure in edge detection. It helps in filtering out edges that are less likely to be part of the edge structure of the image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`high_threshold`**
    - The upper threshold for the hysteresis procedure in edge detection. It is used to identify strong edges in the image, ensuring that only the most significant edges are considered.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The output image after applying the Canny edge detection algorithm. It highlights the edges detected in the input image.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ControlNetApply,PreviewImage,Reroute`


## Source code
```python
class Canny:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"image": ("IMAGE",),
                                "low_threshold": ("FLOAT", {"default": 0.4, "min": 0.01, "max": 0.99, "step": 0.01}),
                                "high_threshold": ("FLOAT", {"default": 0.8, "min": 0.01, "max": 0.99, "step": 0.01})
                                }}

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "detect_edge"

    CATEGORY = "image/preprocessors"

    def detect_edge(self, image, low_threshold, high_threshold):
        output = canny(image.to(comfy.model_management.get_torch_device()).movedim(-1, 1), low_threshold, high_threshold)
        img_out = output[1].to(comfy.model_management.intermediate_device()).repeat(1, 3, 1, 1).movedim(1, -1)
        return (img_out,)

```
