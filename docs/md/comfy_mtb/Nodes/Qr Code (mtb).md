---
tags:
- ImageDrawing
---

# Qr Code (mtb)
## Documentation
- Class name: `Qr Code (mtb)`
- Category: `mtb/generate`
- Output node: `False`

The MTB_QrCode node is designed for generating QR codes based on user-defined parameters such as URL, size, error correction level, and color inversion. It emphasizes ease of customization for QR code generation, catering to a variety of use cases where QR codes are needed.
## Input types
### Required
- **`url`**
    - Specifies the URL to be encoded in the QR code, serving as the primary content of the QR code.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`width`**
    - Determines the width of the generated QR code image in pixels, allowing for size customization.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`height`**
    - Determines the height of the generated QR code image in pixels, enabling size customization similar to width.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`error_correct`**
    - Sets the error correction level for the QR code, affecting its resilience to damage.
    - Comfy dtype: `['L', 'M', 'Q', 'H']`
    - Python dtype: `str`
- **`box_size`**
    - Specifies the size of each box in the QR code grid, impacting the overall density and size of the QR code.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`border`**
    - Defines the width of the border around the QR code, ensuring it is distinguishable from its surroundings.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`invert`**
    - Allows for the inversion of the QR code's color scheme, offering visual customization.
    - Comfy dtype: `['BOOLEAN']`
    - Python dtype: `bool`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - Returns the generated QR code as an image, ready for use in various applications.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class MTB_QrCode:
    """Basic QR Code generator"""

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "url": ("STRING", {"default": "https://www.github.com"}),
                "width": (
                    "INT",
                    {"default": 256, "max": 8096, "min": 0, "step": 1},
                ),
                "height": (
                    "INT",
                    {"default": 256, "max": 8096, "min": 0, "step": 1},
                ),
                "error_correct": (("L", "M", "Q", "H"), {"default": "L"}),
                "box_size": (
                    "INT",
                    {"default": 10, "max": 8096, "min": 0, "step": 1},
                ),
                "border": (
                    "INT",
                    {"default": 4, "max": 8096, "min": 0, "step": 1},
                ),
                "invert": (("BOOLEAN",), {"default": False}),
            }
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "do_qr"
    CATEGORY = "mtb/generate"

    def do_qr(
        self, url, width, height, error_correct, box_size, border, invert
    ):
        log.warning(
            "This node will soon be deprecated, there are much better alternatives like https://github.com/coreyryanhanson/comfy-qr"
        )
        if error_correct == "L" or error_correct not in ["M", "Q", "H"]:
            error_correct = qrcode.constants.ERROR_CORRECT_L
        elif error_correct == "M":
            error_correct = qrcode.constants.ERROR_CORRECT_M
        elif error_correct == "Q":
            error_correct = qrcode.constants.ERROR_CORRECT_Q
        else:
            error_correct = qrcode.constants.ERROR_CORRECT_H

        qr = qrcode.QRCode(
            version=1,
            error_correction=error_correct,
            box_size=box_size,
            border=border,
        )
        qr.add_data(url)
        qr.make(fit=True)

        back_color = (255, 255, 255) if invert else (0, 0, 0)
        fill_color = (0, 0, 0) if invert else (255, 255, 255)

        code = img = qr.make_image(
            back_color=back_color, fill_color=fill_color
        )

        # that we now resize without filtering
        code = code.resize((width, height), Image.NEAREST)

        return (pil2tensor(code),)

```
