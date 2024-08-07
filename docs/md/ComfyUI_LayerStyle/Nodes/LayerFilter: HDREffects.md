---
tags:
- VisualEffects
---

# LayerFilter: HDR Effects
## Documentation
- Class name: `LayerFilter: HDREffects`
- Category: `😺dzNodes/LayerFilter`
- Output node: `False`

The HDR Effects node is designed to enhance images by applying High Dynamic Range (HDR) effects, improving contrast, brightness, and color depth to simulate the HDR photography technique. This node aims to make images appear more vivid and closer to what the human eye perceives in real life, by adjusting various parameters such as shadow, highlight, and gamma intensity.
## Input types
### Required
- **`image`**
    - The input image to which HDR effects will be applied. This is the primary input for the node, serving as the basis for all subsequent HDR enhancements.
    - Comfy dtype: `IMAGE`
    - Python dtype: `PIL.Image or torch.Tensor`
- **`hdr_intensity`**
    - Controls the overall intensity of the HDR effect, affecting how pronounced the HDR enhancements are on the image.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`shadow_intensity`**
    - Adjusts the intensity of shadows in the image, making them lighter or darker to enhance depth and detail.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`highlight_intensity`**
    - Adjusts the intensity of highlights in the image, enhancing brightness in lighter areas for a more dynamic range.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`gamma_intensity`**
    - Controls the gamma correction level, affecting the mid-tones and overall brightness of the image.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`contrast`**
    - Adjusts the contrast level of the image, enhancing the difference between light and dark areas.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`enhance_color`**
    - Enhances the saturation and vibrancy of colors in the image, making them more vivid and pronounced.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The output image after applying HDR effects, showcasing enhanced contrast, brightness, and color depth.
    - Python dtype: `PIL.Image or torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LS_HDREffects:
    @classmethod
    def INPUT_TYPES(cls):
        return {'required': {'image': ('IMAGE', {'default': None}),
                             'hdr_intensity': ('FLOAT', {'default': 0.5, 'min': 0.0, 'max': 5.0, 'step': 0.01}),
                             'shadow_intensity': ('FLOAT', {'default': 0.25, 'min': 0.0, 'max': 1.0, 'step': 0.01}),
                             'highlight_intensity': ('FLOAT', {'default': 0.75, 'min': 0.0, 'max': 1.0, 'step': 0.01}),
                             'gamma_intensity': ('FLOAT', {'default': 0.25, 'min': 0.0, 'max': 1.0, 'step': 0.01}),
                             'contrast': ('FLOAT', {'default': 0.1, 'min': 0.0, 'max': 1.0, 'step': 0.01}),
                             'enhance_color': ('FLOAT', {'default': 0.25, 'min': 0.0, 'max': 1.0, 'step': 0.01})
                             }}

    RETURN_TYPES = ('IMAGE',)
    RETURN_NAMES = ('image',)
    FUNCTION = 'hdr_effects'
    CATEGORY = '😺dzNodes/LayerFilter'

    @apply_to_batch
    def hdr_effects(self, image, hdr_intensity=0.5, shadow_intensity=0.25, highlight_intensity=0.75,
                   gamma_intensity=0.25, contrast=0.1, enhance_color=0.25):
        # Load the image
        img = tensor2pil(image)

        # Step 1: Convert RGB to LAB for better color preservation
        img_lab = ImageCms.profileToProfile(img, sRGB_profile, Lab_profile, outputMode='LAB')

        # Extract L, A, and B channels
        luminance, a, b = img_lab.split()

        # Convert luminance to a NumPy array for processing
        lum_array = np.array(luminance, dtype=np.float32)

        # Preparing adjustment layers (shadows, midtones, highlights)
        # This example assumes you have methods to extract or calculate these adjustments
        shadows_adjusted = adjust_shadows_non_linear(luminance, shadow_intensity)
        highlights_adjusted = adjust_highlights_non_linear(luminance, highlight_intensity)

        merged_adjustments = merge_adjustments_with_blend_modes(lum_array, shadows_adjusted, highlights_adjusted,
                                                                hdr_intensity, shadow_intensity, highlight_intensity)

        # Apply gamma correction with a base_gamma value (define based on desired effect)
        gamma_corrected = apply_gamma_correction(np.array(merged_adjustments), hdr_intensity, gamma_intensity)

        # Merge L channel back with original A and B channels
        adjusted_lab = Image.merge('LAB', (merged_adjustments, a, b))

        # Step 3: Convert LAB back to RGB
        img_adjusted = ImageCms.profileToProfile(adjusted_lab, Lab_profile, sRGB_profile, outputMode='RGB')

        # Enhance contrast
        enhancer = ImageEnhance.Contrast(img_adjusted)
        contrast_adjusted = enhancer.enhance(1 + contrast)

        # Enhance color saturation
        enhancer = ImageEnhance.Color(contrast_adjusted)
        color_adjusted = enhancer.enhance(1 + enhance_color * 0.2)

        return pil2tensor(color_adjusted)

```
