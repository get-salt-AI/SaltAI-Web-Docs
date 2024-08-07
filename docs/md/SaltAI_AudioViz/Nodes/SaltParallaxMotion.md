---
tags:
- Scheduling
- VisualEffects
---

# Parallax Motion Parameter Schedule Generator
## Documentation
- Class name: `SaltParallaxMotion`
- Category: `SALT/AudioViz/Scheduling/Parallax Motion`
- Output node: `False`

The SaltParallaxMotion node is designed to generate a schedule for parallax motion parameters, enabling dynamic visual effects in audio-visual presentations. It leverages Perlin noise and various movement modes to create complex, organic motion patterns that enhance the depth and immersion of visual content.
## Input types
### Required
- **`zoom_preset`**
    - Specifies the zoom behavior for the parallax motion, allowing selection from predefined options or custom settings to tailor the visual zoom effect.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`horizontal_pan_preset`**
    - Determines the horizontal pan direction and behavior, offering a range of predefined or custom options to control the horizontal movement.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`vertical_pan_preset`**
    - Defines the vertical pan direction and behavior, with options for predefined or custom settings to manage the vertical movement.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`custom_x_min`**
    - The minimum custom horizontal position, enabling precise control over the horizontal aspect of the parallax motion.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`custom_x_max`**
    - The maximum custom horizontal position, allowing for detailed customization of the horizontal movement extent.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`custom_y_min`**
    - The minimum custom vertical position, facilitating fine-tuned control over the vertical aspect of the parallax motion.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`custom_y_max`**
    - The maximum custom vertical position, enabling detailed adjustment of the vertical movement range.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`custom_z_min`**
    - The minimum custom depth position, providing control over the depth aspect of the parallax motion.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`custom_z_max`**
    - The maximum custom depth position, allowing for precise depth customization in the parallax effect.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`parallax_intensity`**
    - Determines the intensity of the parallax effect, influencing how much the background moves relative to the foreground to create a sense of depth.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`zoom_intensity`**
    - Controls the intensity of the zoom effect, adjusting how much the zoom changes between the foreground and background layers to enhance the parallax effect.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`front_x_min`**
    - Comfy dtype: `FLOAT`
    - The minimum X position for the front layer, part of the calculated parallax motion parameters.
    - Python dtype: `float`
- **`front_x_max`**
    - Comfy dtype: `FLOAT`
    - The maximum X position for the front layer, part of the calculated parallax motion parameters.
    - Python dtype: `float`
- **`front_y_min`**
    - Comfy dtype: `FLOAT`
    - The minimum Y position for the front layer, part of the calculated parallax motion parameters.
    - Python dtype: `float`
- **`front_y_max`**
    - Comfy dtype: `FLOAT`
    - The maximum Y position for the front layer, part of the calculated parallax motion parameters.
    - Python dtype: `float`
- **`front_z_min`**
    - Comfy dtype: `FLOAT`
    - The minimum Z position for the front layer, indicating the depth of zoom at the start of the parallax motion.
    - Python dtype: `float`
- **`front_z_max`**
    - Comfy dtype: `FLOAT`
    - The maximum Z position for the front layer, indicating the depth of zoom at the end of the parallax motion.
    - Python dtype: `float`
- **`back_x_min`**
    - Comfy dtype: `FLOAT`
    - The minimum X position for the back layer, calculated to create a differential motion effect with the front layer.
    - Python dtype: `float`
- **`back_x_max`**
    - Comfy dtype: `FLOAT`
    - The maximum X position for the back layer, calculated to create a differential motion effect with the front layer.
    - Python dtype: `float`
- **`back_y_min`**
    - Comfy dtype: `FLOAT`
    - The minimum Y position for the back layer, calculated to enhance the depth effect in the parallax motion.
    - Python dtype: `float`
- **`back_y_max`**
    - Comfy dtype: `FLOAT`
    - The maximum Y position for the back layer, calculated to enhance the depth effect in the parallax motion.
    - Python dtype: `float`
- **`back_z_min`**
    - Comfy dtype: `FLOAT`
    - The minimum Z position for the back layer, kept constant to maintain a consistent background depth throughout the parallax motion.
    - Python dtype: `float`
- **`back_z_max`**
    - Comfy dtype: `FLOAT`
    - The maximum Z position for the back layer, kept constant to maintain a consistent background depth throughout the parallax motion.
    - Python dtype: `float`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaltParallaxMotion:
    """
    A node for generating min/max FLOAT values for Front and Back layers across X, Y, and Z axes
    in 2D plane translations to create a parallax effect, based on selected presets or custom input values.
    Adjusts these values based on the parallax intensity.
    """
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "zoom_preset": (["None", "Zoom In", "Zoom Out", "Zoom In/Out", "Zoom Out/In", "Custom", "Select Random"], {
                    "default": "None"
                }),
                "horizontal_pan_preset": (["None", "Pan Left → Right", "Pan Right → Left", "Pan Left → Center", 
                                        "Pan Right → Center", "Pan Center → Right", "Pan Center → Left", "Custom", "Select Random"], {
                    "default": "None"
                }),
                "vertical_pan_preset": (["None", "Pan Up → Down", "Pan Down → Up", "Pan Up → Center", 
                                        "Pan Down → Center", "Pan Center → Up", "Pan Center → Down", "Custom", "Select Random"], {
                    "default": "None"
                }),
                "custom_x_min": ("FLOAT", {"default": 0.0}),
                "custom_x_max": ("FLOAT", {"default": 0.0}),
                "custom_y_min": ("FLOAT", {"default": 0.0}),
                "custom_y_max": ("FLOAT", {"default": 0.0}),
                "custom_z_min": ("FLOAT", {"default": 1.0}),
                "custom_z_max": ("FLOAT", {"default": 1.0}),
                "parallax_intensity": ("FLOAT", {"default": 1.0}),
                "zoom_intensity": ("FLOAT", {"default": 1.0, "min": 0.1, "max": 10.0}),
            },
        }

    RETURN_TYPES = ("FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT", "FLOAT")
    RETURN_NAMES = ("front_x_min", "front_x_max", "front_y_min", "front_y_max", "front_z_min", "front_z_max", "back_x_min", "back_x_max", "back_y_min", "back_y_max", "back_z_min", "back_z_max")
    FUNCTION = "generate_parameters"
    CATEGORY = f"{MENU_NAME}/{SUB_MENU_NAME}/Scheduling/Parallax Motion"

    def generate_parameters(self, zoom_preset, horizontal_pan_preset, vertical_pan_preset, 
                        custom_x_min, custom_x_max, custom_y_min, custom_y_max, 
                        custom_z_min, custom_z_max, parallax_intensity, zoom_intensity):
            # Handle random selection for zoom presets
        if zoom_preset == "Select Random":
            zoom_options = [key for key in zoom_presets.keys() if key not in ["None", "Custom", "Select Random"]]
            zoom_preset = random.choice(zoom_options)
        # Apply the selected or randomly chosen zoom preset
        if zoom_preset in zoom_presets:
            z_min, z_max = zoom_presets[zoom_preset]

        # Handle random selection for horizontal pan presets
        if horizontal_pan_preset == "Select Random":
            horizontal_options = [key for key in horizontal_pan_presets.keys() if key not in ["None", "Custom", "Select Random"]]
            horizontal_pan_preset = random.choice(horizontal_options)
        # Apply the selected or randomly chosen horizontal pan preset
        if horizontal_pan_preset in horizontal_pan_presets:
            x_min, x_max = horizontal_pan_presets[horizontal_pan_preset]

        # Handle random selection for vertical pan presets
        if vertical_pan_preset == "Select Random":
            vertical_options = [key for key in vertical_pan_presets.keys() if key not in ["None", "Custom", "Select Random"]]
            vertical_pan_preset = random.choice(vertical_options)
        # Apply the selected or randomly chosen vertical pan preset
        if vertical_pan_preset in vertical_pan_presets:
            y_min, y_max = vertical_pan_presets[vertical_pan_preset]
        
        # Initialize default axis values
        x_min, x_max = 0, 0
        y_min, y_max = 0, 0
        z_min, z_max = 1, 1  # Default Z values assume no zoom (scale of 1)
        
        # Apply the selected zoom preset
        if zoom_preset in zoom_presets:
            z_min, z_max = zoom_presets[zoom_preset]
        
        # Apply the selected horizontal pan preset
        if horizontal_pan_preset in horizontal_pan_presets:
            x_min, x_max = horizontal_pan_presets[horizontal_pan_preset]
        
        # Apply the selected vertical pan preset
        if vertical_pan_preset in vertical_pan_presets:
            y_min, y_max = vertical_pan_presets[vertical_pan_preset]
        
        # For 'Custom' selections, override with custom values
        if zoom_preset == "Custom":
            z_min, z_max = custom_z_min, custom_z_max
        if horizontal_pan_preset == "Custom":
            x_min, x_max = custom_x_min, custom_x_max
        if vertical_pan_preset == "Custom":
            y_min, y_max = custom_y_min, custom_y_max

        # Calculate the back layer's values based on the parallax intensity
        back_x_min = x_min / parallax_intensity if x_min != 0 else 0
        back_x_max = x_max / parallax_intensity if x_max != 0 else 0
        back_y_min = y_min / parallax_intensity if y_min != 0 else 0
        back_y_max = y_max / parallax_intensity if y_max != 0 else 0
        # Z values are not adjusted by parallax intensity
        back_z_min, back_z_max = z_min, z_max

        # Adjust for zoom intensity for Z axis to increase front zoom relative to back with higher intensity values
        # This approach assumes the front layer's zoom can increase more than the back's as zoom_intensity increases.
        # Adjust the formula as needed to align with your specific vision for the effect.
        adjusted_front_z_min = z_min * (1 + zoom_intensity - 1)  # Example adjustment, enhances the front zoom based on intensity
        adjusted_front_z_max = z_max * (1 + zoom_intensity - 1)  # Similarly for max zoom

        # The back layer's zoom could remain unchanged, or you could apply a different formula if the back layer also needs adjustment.
        back_z_min, back_z_max = z_min, z_max  # Keeps back layer's zoom unchanged; modify as needed.


        return (x_min, x_max, y_min, y_max, adjusted_front_z_min, adjusted_front_z_max, back_x_min, back_x_max, back_y_min, back_y_max, back_z_min, back_z_max)

```
