---
tags:
- Mask
- MaskGeneration
---

# Create Text Mask
## Documentation
- Class name: `CreateTextMask`
- Category: `KJNodes/text`
- Output node: `False`

The CreateTextMask node is designed to generate text-based masks for images. It allows for the customization of text appearance and positioning within a specified frame, enabling the creation of dynamic and visually engaging masks based on textual content.
## Input types
### Required
- **`invert`**
    - Determines whether the text mask should be inverted. This affects the visual contrast between the text and the background in the generated mask.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`frames`**
    - Specifies the number of frames for the animation. This parameter is essential for creating animated masks, defining the total length of the animation.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`text_x`**
    - Sets the horizontal position of the text within the frame. This parameter allows precise control over the text's location in the mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`text_y`**
    - Sets the vertical position of the text within the frame. This parameter allows precise control over the text's location in the mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`font_size`**
    - Determines the size of the font used for the text in the mask. This parameter allows for the adjustment of the text's visual impact within the mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`font_color`**
    - Specifies the color of the text in the mask. This parameter enables the customization of the text's visual appearance to match the desired aesthetic.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`text`**
    - Defines the text to be displayed in the mask. This parameter allows for the input of custom text, making each mask uniquely tailored to the desired content.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`font`**
    - Selects the font used for the text in the mask. This parameter enables customization of the text's appearance by choosing from a variety of fonts.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `list`
- **`width`**
    - Sets the width of the frame in which the text mask will be generated. This parameter defines the horizontal bounds for the text placement.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`height`**
    - Sets the height of the frame in which the text mask will be generated. This parameter defines the vertical bounds for the text placement.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`start_rotation`**
    - Specifies the starting rotation angle for the text in the mask. This parameter is crucial for creating dynamic, rotating text effects in the mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`end_rotation`**
    - Specifies the ending rotation angle for the text in the mask, enabling the creation of rotating text animations between the start and end angles.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The generated image with the text mask applied.
    - Python dtype: `torch.Tensor`
- **`mask`**
    - Comfy dtype: `MASK`
    - The mask created from the specified text.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CreateTextMask:

    RETURN_TYPES = ("IMAGE", "MASK",)
    FUNCTION = "createtextmask"
    CATEGORY = "KJNodes/text"
    DESCRIPTION = """
Creates a text image and mask.  
Looks for fonts from this folder:  
ComfyUI/custom_nodes/ComfyUI-KJNodes/fonts
  
If start_rotation and/or end_rotation are different values,  
creates animation between them.
"""

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                 "invert": ("BOOLEAN", {"default": False}),
                 "frames": ("INT", {"default": 1,"min": 1, "max": 4096, "step": 1}),
                 "text_x": ("INT", {"default": 0,"min": 0, "max": 4096, "step": 1}),
                 "text_y": ("INT", {"default": 0,"min": 0, "max": 4096, "step": 1}),
                 "font_size": ("INT", {"default": 32,"min": 8, "max": 4096, "step": 1}),
                 "font_color": ("STRING", {"default": "white"}),
                 "text": ("STRING", {"default": "HELLO!", "multiline": True}),
                 "font": (folder_paths.get_filename_list("kjnodes_fonts"), ),
                 "width": ("INT", {"default": 512,"min": 16, "max": 4096, "step": 1}),
                 "height": ("INT", {"default": 512,"min": 16, "max": 4096, "step": 1}),
                 "start_rotation": ("INT", {"default": 0,"min": 0, "max": 359, "step": 1}),
                 "end_rotation": ("INT", {"default": 0,"min": -359, "max": 359, "step": 1}),
        },
    } 

    def createtextmask(self, frames, width, height, invert, text_x, text_y, text, font_size, font_color, font, start_rotation, end_rotation):
    # Define the number of images in the batch
        batch_size = frames
        out = []
        masks = []
        rotation = start_rotation
        if start_rotation != end_rotation:
            rotation_increment = (end_rotation - start_rotation) / (batch_size - 1)

        font_path = folder_paths.get_full_path("kjnodes_fonts", font)
        # Generate the text
        for i in range(batch_size):
            image = Image.new("RGB", (width, height), "black")
            draw = ImageDraw.Draw(image)
            font = ImageFont.truetype(font_path, font_size)
            
            # Split the text into words
            words = text.split()
            
            # Initialize variables for line creation
            lines = []
            current_line = []
            current_line_width = 0
            try: #new pillow  
                # Iterate through words to create lines
                for word in words:
                    word_width = font.getbbox(word)[2]
                    if current_line_width + word_width <= width - 2 * text_x:
                        current_line.append(word)
                        current_line_width += word_width + font.getbbox(" ")[2] # Add space width
                    else:
                        lines.append(" ".join(current_line))
                        current_line = [word]
                        current_line_width = word_width
            except: #old pillow             
                for word in words:
                    word_width = font.getsize(word)[0]
                    if current_line_width + word_width <= width - 2 * text_x:
                        current_line.append(word)
                        current_line_width += word_width + font.getsize(" ")[0] # Add space width
                    else:
                        lines.append(" ".join(current_line))
                        current_line = [word]
                        current_line_width = word_width
            
            # Add the last line if it's not empty
            if current_line:
                lines.append(" ".join(current_line))
            
            # Draw each line of text separately
            y_offset = text_y
            for line in lines:
                text_width = font.getlength(line)
                text_height = font_size
                text_center_x = text_x + text_width / 2
                text_center_y = y_offset + text_height / 2
                try:
                    draw.text((text_x, y_offset), line, font=font, fill=font_color, features=['-liga'])
                except:
                    draw.text((text_x, y_offset), line, font=font, fill=font_color)
                y_offset += text_height # Move to the next line
            
            if start_rotation != end_rotation:
                image = image.rotate(rotation, center=(text_center_x, text_center_y))
                rotation += rotation_increment
            
            image = np.array(image).astype(np.float32) / 255.0
            image = torch.from_numpy(image)[None,]
            mask = image[:, :, :, 0] 
            masks.append(mask)
            out.append(image)
            
        if invert:
            return (1.0 - torch.cat(out, dim=0), 1.0 - torch.cat(masks, dim=0),)
        return (torch.cat(out, dim=0),torch.cat(masks, dim=0),)

```
