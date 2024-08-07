---
tags:
- ImageTransformation
---

# RemapWarpPolar
## Documentation
- Class name: `RemapWarpPolar`
- Category: `Bmad/CV/Transform`
- Output node: `False`

The RemapWarpPolar node is designed to transform images by applying a warp polar transformation. This process involves converting images from a rectangular to a polar coordinate system, optionally applying logarithmic scaling, and performing inverse transformations. It supports adjustments to the transformation center, radius, and cropping of the result, making it versatile for various image processing tasks.
## Input types
### Required
- **`max_radius`**
    - Specifies the maximum radius for the warp polar transformation, affecting the extent of the transformation applied to the image.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `int`
- **`radius_adjust`**
    - Adjusts the effective radius of the transformation, allowing for fine-tuning of the warp effect.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`center_x_adjust`**
    - Adjusts the x-coordinate of the transformation center, enabling horizontal shifting of the warp effect's focal point.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`center_y_adjust`**
    - Adjusts the y-coordinate of the transformation center, enabling vertical shifting of the warp effect's focal point.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`log`**
    - Determines whether logarithmic scaling is applied, changing the nature of the warp from linear to logarithmic.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`inverse`**
    - Controls whether the inverse warp polar transformation is applied, allowing for the reversal of the warp effect.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`crop`**
    - Enables cropping of the transformed image to remove areas outside the specified radius, focusing on the central area.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`remap`**
    - Comfy dtype: `REMAP`
    - The result of the warp polar transformation, including any adjustments and cropping applied to the original image.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class RemapWarpPolar(RemapBase):
    MAX_RADIUS = {
        "half min shape": lambda shape: np.min(shape[:2]) / 2,
        "half max shape": lambda shape: np.max(shape[:2]) / 2,
        "hypot": lambda shape: np.hypot(shape[1] / 2, shape[0] / 2),
        "raw": lambda _: 1  # uses value set by radius_adjust
    }
    MAX_RADIUS_KEYS = list(MAX_RADIUS.keys())

    @classmethod
    def INPUT_TYPES(cls):
        return {"required": {
            "max_radius": (cls.MAX_RADIUS_KEYS, {"default": cls.MAX_RADIUS_KEYS[0]}),
            "radius_adjust": ("FLOAT", {"default": 1, "min": .1, "max": 2048, "step": 0.01}),
            "center_x_adjust": ("FLOAT", {"default": 0, "min": -3, "max": 3, "step": 0.01}),
            "center_y_adjust": ("FLOAT", {"default": 0, "min": -3, "max": 3, "step": 0.01}),
            "log": ("BOOLEAN", {"default": False}),
            "inverse": ("BOOLEAN", {"default": True}),
            "crop": ("BOOLEAN", {"default": True})
        }
        }

    @staticmethod
    def warp(custom_data, src, interpolation, mask=None):
        max_radius, radius_adj, center_x_adj, center_y_adj, log, inverse, crop = custom_data

        center = (
        src.shape[1] / 2 + src.shape[1] / 2 * center_x_adj, src.shape[0] / 2 + src.shape[0] / 2 * center_y_adj)
        radius = RemapWarpPolar.MAX_RADIUS[max_radius](src.shape) * radius_adj
        flags = interpolation | cv.WARP_FILL_OUTLIERS
        flags |= cv.WARP_POLAR_LOG if log else cv.WARP_POLAR_LINEAR
        if inverse:
            flags |= cv.WARP_INVERSE_MAP

        img = cv.warpPolar(src, (src.shape[1], src.shape[0]), center, radius, flags)
        if mask is not None:
            mask = cv.warpPolar(mask, (mask.shape[1], mask.shape[0]), center, radius, flags)

        if crop:
            left, right = int(max(center[0] - radius, 0)), int(min(center[0] + radius, src.shape[1]))
            top, bottom = int(max(center[1] - radius, 0)), int(min(center[1] + radius, src.shape[0]))
            img = img[top:bottom, left:right]
            mask = mask[top:bottom, left:right]

        return img, mask, None

    def send_remap(self, max_radius, radius_adjust, center_x_adjust, center_y_adjust, log, inverse, crop):
        remap_data = {
            "func": lambda _, mr, ra, cx, cy, l, i, c: (mr, ra, cx, cy, l, i, c),  # does nothing, just returns args
            "xargs": [max_radius, radius_adjust, center_x_adjust, center_y_adjust, log, inverse, crop],
            "custom": RemapWarpPolar.warp
        }
        return (remap_data,)

```
