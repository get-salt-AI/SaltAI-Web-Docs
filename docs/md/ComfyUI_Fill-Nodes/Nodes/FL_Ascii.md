---
tags:
- AnimationScheduling
- VisualEffects
---

# FL Ascii
## Documentation
- Class name: `FL_Ascii`
- Category: `🏵️Fill Nodes`
- Output node: `False`

The FL_Ascii node transforms images into ASCII art by converting the pixel values of an image into characters based on their brightness. This process involves resizing the image, mapping pixel brightness to a set of specified characters, and rendering the result as a new image that visually represents the original image in ASCII form.
## Input types
### Required
- **`image`**
    - The input image to be transformed into ASCII art. It serves as the base for the ASCII conversion process, determining the visual outcome of the ASCII art.
    - Comfy dtype: `IMAGE`
    - Python dtype: `torch.Tensor`
- **`spacing`**
    - Defines the spacing between characters in the ASCII art. It affects the density and readability of the resulting ASCII image.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`font_size`**
    - Specifies the font size used for the characters in the ASCII art. This parameter influences the detail and clarity of the ASCII representation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`characters`**
    - A string of characters used to represent different brightness levels in the ASCII art. The choice of characters affects the texture and visual style of the output.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output image rendered as ASCII art. It represents the original image using a specified set of characters, offering a unique, text-based visual interpretation.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class FL_Ascii:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image": ("IMAGE",),
                "spacing": ("INT", {
                    "default": 20,
                    "min": 4,
                    "max": 100,
                    "step": 2,
                }),
                "font_size": ("INT", {
                    "default": 20,
                    "min": 4,
                    "max": 100,
                    "step": 2,
                }),
                "characters": ("STRING", {
                    "default": "\._♥♦♣MachineDelusions♣♦♥_./",
                    "description": "characters to use"
                }),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "apply_ascii_art_effect"
    CATEGORY = "🏵️Fill Nodes"

    def apply_ascii_art_effect(self, image: torch.Tensor, spacing: int, font_size: int, characters):
        batch_size, height, width, channels = image.shape
        result = torch.zeros_like(image)

        for b in range(batch_size):
            img_b = image[b] * 255.0
            img_b = Image.fromarray(img_b.numpy().astype('uint8'), 'RGB')
            result_b = ascii_art_effect(img_b, spacing, font_size, characters)
            result_b = torch.tensor(np.array(result_b)) / 255.0
            result[b] = result_b

            # Update the print log
            progress = (b + 1) / batch_size * 100
            sys.stdout.write(f"\rProcessing images: {progress:.2f}%")
            sys.stdout.flush()

        # Print a new line after the progress log
        print()

        return (result,)

```
