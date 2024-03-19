# ImageQuantize
## Documentation
- Class name: `ImageQuantize`
- Category: `image/postprocessing`
- Output node: `False`

The `ImageQuantize` node performs color quantization on an input image tensor, reducing the number of colors to a specified amount. This process can include dithering to reduce color banding, with support for different dithering techniques including none, Floyd-Steinberg, and Bayer dithering.
## Input types
### Required
- **`image`**
    - The input image tensor to be quantized. The quantization process reduces the color palette of this image to the specified number of colors, potentially applying dithering to improve visual quality.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`colors`**
    - Specifies the number of colors to reduce the input image to during the quantization process. This directly impacts the visual fidelity and the size of the resulting image.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`dither`**
    - Determines the dithering technique to be applied during quantization, which can help in reducing color banding effects. Supported options include 'none', 'floyd-steinberg', and various orders of 'bayer' dithering.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`image`**
    - The quantized image tensor with reduced color palette. This output maintains the original dimensions of the input image but with a limited number of colors.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class Quantize:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "image": ("IMAGE",),
                "colors": ("INT", {
                    "default": 256,
                    "min": 1,
                    "max": 256,
                    "step": 1
                }),
                "dither": (["none", "floyd-steinberg", "bayer-2", "bayer-4", "bayer-8", "bayer-16"],),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "quantize"

    CATEGORY = "image/postprocessing"

    def bayer(im, pal_im, order):
        def normalized_bayer_matrix(n):
            if n == 0:
                return np.zeros((1,1), "float32")
            else:
                q = 4 ** n
                m = q * normalized_bayer_matrix(n - 1)
                return np.bmat(((m-1.5, m+0.5), (m+1.5, m-0.5))) / q

        num_colors = len(pal_im.getpalette()) // 3
        spread = 2 * 256 / num_colors
        bayer_n = int(math.log2(order))
        bayer_matrix = torch.from_numpy(spread * normalized_bayer_matrix(bayer_n) + 0.5)

        result = torch.from_numpy(np.array(im).astype(np.float32))
        tw = math.ceil(result.shape[0] / bayer_matrix.shape[0])
        th = math.ceil(result.shape[1] / bayer_matrix.shape[1])
        tiled_matrix = bayer_matrix.tile(tw, th).unsqueeze(-1)
        result.add_(tiled_matrix[:result.shape[0],:result.shape[1]]).clamp_(0, 255)
        result = result.to(dtype=torch.uint8)

        im = Image.fromarray(result.cpu().numpy())
        im = im.quantize(palette=pal_im, dither=Image.Dither.NONE)
        return im

    def quantize(self, image: torch.Tensor, colors: int, dither: str):
        batch_size, height, width, _ = image.shape
        result = torch.zeros_like(image)

        for b in range(batch_size):
            im = Image.fromarray((image[b] * 255).to(torch.uint8).numpy(), mode='RGB')

            pal_im = im.quantize(colors=colors) # Required as described in https://github.com/python-pillow/Pillow/issues/5836

            if dither == "none":
                quantized_image = im.quantize(palette=pal_im, dither=Image.Dither.NONE)
            elif dither == "floyd-steinberg":
                quantized_image = im.quantize(palette=pal_im, dither=Image.Dither.FLOYDSTEINBERG)
            elif dither.startswith("bayer"):
                order = int(dither.split('-')[-1])
                quantized_image = Quantize.bayer(im, pal_im, order)

            quantized_array = torch.tensor(np.array(quantized_image.convert("RGB"))).float() / 255
            result[b] = quantized_array

        return (result,)

```
