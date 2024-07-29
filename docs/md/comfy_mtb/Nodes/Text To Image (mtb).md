---
tags:
- ImageDrawing
- PanelDesign
- TextOnImage
- VisualEffects
---

# Text To Image (mtb)
## Documentation
- Class name: `Text To Image (mtb)`
- Category: `mtb/generate`
- Output node: `False`

The Text To Image node is designed for the dynamic generation of text-based images, leveraging a variety of fonts to visually represent textual content. It enables the creation of customized images by adjusting text appearance, alignment, and color settings, catering to diverse applications such as content visualization, labeling, or user interface design.
## Input types
### Required
- **`text`**
    - The text string to be converted into an image, serving as the primary content for visual representation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`font`**
    - The font used for the text in the image, selected from a list of available fonts, enabling customization of text appearance.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`wrap`**
    - Controls whether the text should wrap within the image boundaries, affecting text distribution across lines.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`trim`**
    - Determines whether to trim whitespace around the text, adjusting text positioning and image aesthetics.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`line_height`**
    - Adjusts the height between lines of text, affecting text spacing for readability or design requirements.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`font_size`**
    - The size of the font for the text, influencing the text's visual prominence within the image.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`width`**
    - The width of the image, defining the horizontal boundary for the text and any wrapping behavior.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`height`**
    - The height of the image, defining the vertical space for text layout.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`color`**
    - The color of the text, enabling visual customization to match design themes or preferences.
    - Comfy dtype: `COLOR`
    - Python dtype: `str`
- **`background`**
    - The background color of the image, set to complement or contrast with the text color.
    - Comfy dtype: `COLOR`
    - Python dtype: `str`
- **`h_align`**
    - The horizontal alignment of the text (left, center, right), affecting its positioning relative to image boundaries.
    - Comfy dtype: `['left', 'center', 'right']`
    - Python dtype: `str`
- **`v_align`**
    - The vertical alignment of the text (top, center, bottom), influencing its vertical positioning.
    - Comfy dtype: `['top', 'center', 'bottom']`
    - Python dtype: `str`
- **`h_offset`**
    - The horizontal offset for the text, allowing fine control over its position within the image.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`v_offset`**
    - The vertical offset for the text, enabling precise adjustment of its vertical position.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`h_coverage`**
    - The percentage of image width the text should cover, controlling the text's spread within the image.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The generated image from the input text and customization parameters, visually representing the text according to specified settings.
    - Python dtype: `PIL.Image.Image`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class MTB_TextToImage:
    """Utils to convert text to image using a font.

    The tool looks for any .ttf file in the Comfy folder hierarchy.
    """

    fonts = {}
    DESCRIPTION = """# Text to Image

This node look for any font files in comfy_dir/fonts.  
by default it fallsback to a default font.

![img](https://i.imgur.com/3GT92hy.gif)
"""

    def __init__(self):
        # - This is executed when the graph is executed,
        # - we could conditionaly reload fonts there
        pass

    @classmethod
    def CACHE_FONTS(cls):
        font_extensions = ["*.ttf", "*.otf", "*.woff", "*.woff2", "*.eot"]
        fonts = [font_path]

        for extension in font_extensions:
            try:
                if comfy_dir.exists():
                    fonts.extend(comfy_dir.glob(f"fonts/**/{extension}"))
                else:
                    log.warn(f"Directory {comfy_dir} does not exist.")
            except Exception as e:
                log.error(f"Error during font caching: {e}")

        for font in fonts:
            log.debug(f"Adding font {font}")
            MTB_TextToImage.fonts[font.stem] = font.as_posix()

    @classmethod
    def INPUT_TYPES(cls):
        if not cls.fonts:
            cls.CACHE_FONTS()
        else:
            log.debug(f"Using cached fonts (count: {len(cls.fonts)})")
        return {
            "required": {
                "text": (
                    "STRING",
                    {"default": "Hello world!"},
                ),
                "font": ((sorted(cls.fonts.keys())),),
                "wrap": ("BOOLEAN", {"default": True}),
                "trim": ("BOOLEAN", {"default": True}),
                "line_height": (
                    "FLOAT",
                    {"default": 1.0, "min": 0, "step": 0.1},
                ),
                "font_size": (
                    "INT",
                    {"default": 32, "min": 1, "max": 2500, "step": 1},
                ),
                "width": (
                    "INT",
                    {"default": 512, "min": 1, "max": 8096, "step": 1},
                ),
                "height": (
                    "INT",
                    {"default": 512, "min": 1, "max": 8096, "step": 1},
                ),
                "color": (
                    "COLOR",
                    {"default": "black"},
                ),
                "background": (
                    "COLOR",
                    {"default": "white"},
                ),
                "h_align": (("left", "center", "right"), {"default": "left"}),
                "v_align": (("top", "center", "bottom"), {"default": "top"}),
                "h_offset": (
                    "INT",
                    {"default": 0, "min": 0, "max": 8096, "step": 1},
                ),
                "v_offset": (
                    "INT",
                    {"default": 0, "min": 0, "max": 8096, "step": 1},
                ),
                "h_coverage": (
                    "INT",
                    {"default": 100, "min": 1, "max": 100, "step": 1},
                ),
            }
        }

    RETURN_TYPES = ("IMAGE",)
    RETURN_NAMES = ("image",)
    FUNCTION = "text_to_image"
    CATEGORY = "mtb/generate"

    def text_to_image(
        self,
        text: str,
        font,
        wrap,
        trim,
        line_height,
        font_size,
        width,
        height,
        color,
        background,
        h_align="left",
        v_align="top",
        h_offset=0,
        v_offset=0,
        h_coverage=100,
    ):
        import textwrap

        from PIL import Image, ImageDraw, ImageFont

        font_path = self.fonts[font]

        text = (
            text.encode("ascii", "ignore").decode().strip() if trim else text
        )
        # Handle word wrapping
        if wrap:
            wrap_width = (((width / 100) * h_coverage) / font_size) * 2
            lines = textwrap.wrap(text, width=wrap_width)
        else:
            lines = [text]
        font = ImageFont.truetype(font_path, size=font_size)
        log.debug(f"Lines: {lines}")
        img = Image.new("RGBA", (width, height), background)
        draw = ImageDraw.Draw(img)

        line_height_px = line_height * font_size

        # Vertical alignment
        if v_align == "top":
            y_text = v_offset
        elif v_align == "center":
            y_text = ((height - (line_height_px * len(lines))) // 2) + v_offset
        else:  # bottom
            y_text = (height - (line_height_px * len(lines))) - v_offset

        def get_width(line):
            if hasattr(font, "getsize"):
                return font.getsize(line)[0]
            else:
                return font.getlength(line)

        # Draw each line of text
        for line in lines:
            line_width = get_width(line)
            # Horizontal alignment
            if h_align == "left":
                x_text = h_offset
            elif h_align == "center":
                x_text = ((width - line_width) // 2) + h_offset
            else:  # right
                x_text = (width - line_width) - h_offset

            draw.text((x_text, y_text), line, fill=color, font=font)
            y_text += line_height_px

        return (pil2tensor(img),)

```
