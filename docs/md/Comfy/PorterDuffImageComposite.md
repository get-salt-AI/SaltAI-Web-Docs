# Porter-Duff Image Composite
## Documentation
- Class name: `PorterDuffImageComposite`
- Category: `mask/compositing`
- Output node: `False`

The `PorterDuffImageComposite` node applies various Porter-Duff compositing modes to combine source and destination images and their respective alpha channels. It supports a range of compositing operations such as ADD, CLEAR, DARKEN, etc., each affecting the output image and alpha channel differently based on the specified mode.
## Input types
### Required
- **`source`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`source_alpha`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MASK`
- **`destination`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`destination_alpha`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MASK`
- **`mode`**
    - Specifies the Porter-Duff compositing mode to use for combining the images.
    - Python dtype: `PorterDuffMode`
    - Comfy dtype: `STRING`
## Output types
- **`image`**
    - The composited image resulting from the applied Porter-Duff mode.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The alpha channel of the composited image, indicating its transparency.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PorterDuffImageComposite`


## Source code
```python
class PorterDuffImageComposite:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "source": ("IMAGE",),
                "source_alpha": ("MASK",),
                "destination": ("IMAGE",),
                "destination_alpha": ("MASK",),
                "mode": ([mode.name for mode in PorterDuffMode], {"default": PorterDuffMode.DST.name}),
            },
        }

    RETURN_TYPES = ("IMAGE", "MASK")
    FUNCTION = "composite"
    CATEGORY = "mask/compositing"

    def composite(self, source: torch.Tensor, source_alpha: torch.Tensor, destination: torch.Tensor, destination_alpha: torch.Tensor, mode):
        batch_size = min(len(source), len(source_alpha), len(destination), len(destination_alpha))
        out_images = []
        out_alphas = []

        for i in range(batch_size):
            src_image = source[i]
            dst_image = destination[i]

            assert src_image.shape[2] == dst_image.shape[2] # inputs need to have same number of channels

            src_alpha = source_alpha[i].unsqueeze(2)
            dst_alpha = destination_alpha[i].unsqueeze(2)

            if dst_alpha.shape[:2] != dst_image.shape[:2]:
                upscale_input = dst_alpha.unsqueeze(0).permute(0, 3, 1, 2)
                upscale_output = comfy.utils.common_upscale(upscale_input, dst_image.shape[1], dst_image.shape[0], upscale_method='bicubic', crop='center')
                dst_alpha = upscale_output.permute(0, 2, 3, 1).squeeze(0)
            if src_image.shape != dst_image.shape:
                upscale_input = src_image.unsqueeze(0).permute(0, 3, 1, 2)
                upscale_output = comfy.utils.common_upscale(upscale_input, dst_image.shape[1], dst_image.shape[0], upscale_method='bicubic', crop='center')
                src_image = upscale_output.permute(0, 2, 3, 1).squeeze(0)
            if src_alpha.shape != dst_alpha.shape:
                upscale_input = src_alpha.unsqueeze(0).permute(0, 3, 1, 2)
                upscale_output = comfy.utils.common_upscale(upscale_input, dst_alpha.shape[1], dst_alpha.shape[0], upscale_method='bicubic', crop='center')
                src_alpha = upscale_output.permute(0, 2, 3, 1).squeeze(0)

            out_image, out_alpha = porter_duff_composite(src_image, src_alpha, dst_image, dst_alpha, PorterDuffMode[mode])

            out_images.append(out_image)
            out_alphas.append(out_alpha.squeeze(2))

        result = (torch.stack(out_images), torch.stack(out_alphas))
        return result

```
