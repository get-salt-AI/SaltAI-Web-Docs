---
tags:
- Mask
- MaskGeneration
---

# LayerMask: CreateGradientMask
## Documentation
- Class name: `LayerMask: CreateGradientMask`
- Category: `😺dzNodes/LayerMask`
- Output node: `False`

This node is designed to create gradient masks with customizable dimensions, gradient direction, scale, offset, and opacity. It allows for the creation of nuanced layer masks that can be applied to images for various effects, such as fading or blending.
## Input types
### Required
- **`width`**
    - Specifies the width of the gradient mask to be created. It determines the horizontal dimension of the resulting mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`height`**
    - Specifies the height of the gradient mask to be created. It determines the vertical dimension of the resulting mask.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`gradient_side`**
    - Determines the side from which the gradient starts. This affects the direction of the gradient applied to the mask.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`gradient_scale`**
    - Controls the scale of the gradient effect within the mask, affecting how gradual or steep the gradient appears.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`gradient_offset`**
    - Adjusts the starting point of the gradient within the mask, allowing for shifted gradient effects.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`opacity`**
    - Sets the opacity level of the gradient mask, enabling transparency effects.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`size_as`**
    - Optionally specifies another object's size to match for the gradient mask, providing flexibility in mask dimensions.
    - Comfy dtype: `*`
    - Python dtype: `object`
## Output types
- **`mask`**
    - Comfy dtype: `MASK`
    - The resulting gradient mask, ready for application to images or layers for various visual effects.
    - Python dtype: `object`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CreateGradientMask:

    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(self):
        side = ['bottom', 'top', 'left', 'right', 'center']
        return {
            "required": {
                "width": ("INT", {"default": 512, "min": 4, "max": 99999, "step": 1}),
                "height": ("INT", {"default": 512, "min": 4, "max": 99999, "step": 1}),
                "gradient_side": (side,),
                "gradient_scale": ("INT", {"default": 100, "min": 1, "max": 9999, "step": 1}),
                "gradient_offset": ("INT", {"default": 0, "min": -9999, "max": 9999, "step": 1}),
                "opacity": ("INT", {"default": 100, "min": 0, "max": 100, "step": 1}),
            },
            "optional": {
                "size_as": (any, {}),
            }
        }

    RETURN_TYPES = ("MASK",)
    RETURN_NAMES = ("mask",)
    FUNCTION = 'create_gradient_mask'
    CATEGORY = '😺dzNodes/LayerMask'

    def create_gradient_mask(self, width, height, gradient_side, gradient_scale, gradient_offset, opacity, size_as=None):

        if size_as is not None:
            if size_as.shape[0] > 0:
                _asimage = tensor2pil(size_as[0])
            else:
                _asimage = tensor2pil(size_as)
            width, height = _asimage.size

        _black = Image.new('L', size=(width, height), color='black')
        _white = Image.new('L', size=(width, height), color='white')
        _canvas = copy.deepcopy(_black)
        debug_image1 = copy.deepcopy(_black).convert('RGB')
        debug_image2 = copy.deepcopy(_black).convert('RGB')
        start_color = '#FFFFFF'
        end_color = '#000000'
        if gradient_side == 'bottom':
            _gradient = create_gradient(start_color, end_color, width, height, direction='bottom')
            if gradient_scale != 100:
                _gradient = _gradient.resize((width, int(height * gradient_scale / 100)))
            _canvas.paste(_gradient.convert('L'), box=(0, gradient_offset))
            if gradient_offset > height:
                _canvas = _white
            elif gradient_offset > 0:
                _canvas.paste(_white, box=(0, gradient_offset - height))
        elif gradient_side == 'top':
            _gradient = create_gradient(start_color, end_color, width, height, direction='top')
            if gradient_scale != 100:
                _gradient = _gradient.resize((width, int(height * gradient_scale / 100)))
            _canvas.paste(_gradient.convert('L'), box=(0, height - int(height * gradient_scale / 100) + gradient_offset))
            if gradient_offset < -height:
                _canvas = _white
            elif gradient_offset < 0:
                _canvas.paste(_white, box=(0, height + gradient_offset))
        elif gradient_side == 'left':
            _gradient = create_gradient(start_color, end_color, width, height, direction='left')
            if gradient_scale != 100:
                _gradient = _gradient.resize((int(width * gradient_scale / 100), height))
            _canvas.paste(_gradient.convert('L'), box=(width - int(width * gradient_scale / 100) + gradient_offset, 0))
            if gradient_offset < -width:
                _canvas = _white
            elif gradient_offset < 0:
                _canvas.paste(_white, box=(width + gradient_offset, 0))
        elif gradient_side == 'right':
            _gradient = create_gradient(start_color, end_color, width, height, direction='right')
            if gradient_scale != 100:
                _gradient = _gradient.resize((int(width * gradient_scale / 100), height))
            _canvas.paste(_gradient.convert('L'), box=(gradient_offset, 0))
            if gradient_offset > width:
                _canvas = _white
            elif gradient_offset > 0:
                _canvas.paste(_white, box=(gradient_offset - width, 0))
        else:
            _gradient = create_box_gradient(start_color_inhex='#000000', end_color_inhex='#FFFFFF',
                                            width=width, height=height, scale=int(gradient_scale))
            _gradient = _gradient.convert('L')
            debug_image1 = _gradient
            _blur_mask = Image.new('L', size=(width*2, height*2), color='black')
            _blur_mask.paste(_gradient, box=(int(width/2), int(height/2)))
            _blur_mask = gaussian_blur(_blur_mask, int((width + height) * gradient_scale / 100 / 16))
            _gamma_mask = gamma_trans(_blur_mask, 0.15)
            (crop_x, crop_y, crop_width, crop_height) = mask_area(_gamma_mask)
            crop_box = (crop_x, crop_y, crop_x + crop_width, crop_y + crop_height)
            _blur_mask = _blur_mask.crop(crop_box)
            _blur_mask = _blur_mask.resize((width, height), Image.BILINEAR)
            if gradient_offset != 0:
                resize_width = int(width - gradient_offset)
                resize_height = int(height - gradient_offset)
                if resize_width < 1:
                    resize_width = 1
                if resize_height < 1:
                    resize_height = 1
                _blur_mask = _blur_mask.resize((resize_width, resize_height), Image.BILINEAR)
                paste_box = (int((width - resize_width) / 2), int((height - resize_height) / 2))
            else:
                paste_box = (0,0)
            _canvas.paste(_blur_mask, box=paste_box)
        # opacity
        if opacity < 100:
            _canvas = chop_image(_black, _canvas, 'normal', opacity)
        log(f"{NODE_NAME} Processed.", message_type='finish')
        return (image2mask(_canvas),)

```
