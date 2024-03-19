# HyperTile
## Documentation
- Class name: `HyperTile`
- Category: `model_patches`
- Output node: `False`

The `HyperTile` node is designed to modify the attention mechanism of a given model to enhance its ability to handle images with varying resolutions and aspect ratios. It dynamically adjusts the tiling of the input based on the model's channel configuration and the specified tile size, aiming to optimize the processing of images for tasks that may benefit from attention to finer details or specific regions. This adjustment is made by partitioning the input into smaller, more manageable tiles, potentially improving the model's performance on detailed or high-resolution images.
## Input types
### Required
- **`model`**
    - The model to be patched with the HyperTile functionality. This model is expected to have an attention mechanism that can be modified to incorporate the HyperTile strategy, enhancing its ability to process images with varying resolutions and aspect ratios.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`tile_size`**
    - Specifies the base size for the tiles into which the input image is divided. This size influences how the input is partitioned and can affect the granularity of the attention mechanism's focus.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`swap_size`**
    - Determines the threshold for swapping the dimensions of the tiles, which can be useful for optimizing the processing of images with certain aspect ratios.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`max_depth`**
    - Defines the maximum depth for applying the tiling strategy. This parameter controls how deeply nested the tiling can be, potentially allowing for more detailed attention to specific regions of the input.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`scale_depth`**
    - A boolean flag indicating whether to scale the depth of the tiling based on the model's channel configuration. This can help in adapting the tiling strategy to the specific characteristics of the model and the input.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`model`**
    - The patched model with the HyperTile functionality applied. This model is now capable of handling images with varying resolutions and aspect ratios more effectively, thanks to the dynamic tiling strategy.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PatchModelAddDownscale,FreeU_V2`


## Source code
```python
class HyperTile:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                             "tile_size": ("INT", {"default": 256, "min": 1, "max": 2048}),
                             "swap_size": ("INT", {"default": 2, "min": 1, "max": 128}),
                             "max_depth": ("INT", {"default": 0, "min": 0, "max": 10}),
                             "scale_depth": ("BOOLEAN", {"default": False}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "patch"

    CATEGORY = "model_patches"

    def patch(self, model, tile_size, swap_size, max_depth, scale_depth):
        model_channels = model.model.model_config.unet_config["model_channels"]

        latent_tile_size = max(32, tile_size) // 8
        self.temp = None

        def hypertile_in(q, k, v, extra_options):
            model_chans = q.shape[-2]
            orig_shape = extra_options['original_shape']
            apply_to = []
            for i in range(max_depth + 1):
                apply_to.append((orig_shape[-2] / (2 ** i)) * (orig_shape[-1] / (2 ** i)))

            if model_chans in apply_to:
                shape = extra_options["original_shape"]
                aspect_ratio = shape[-1] / shape[-2]

                hw = q.size(1)
                h, w = round(math.sqrt(hw * aspect_ratio)), round(math.sqrt(hw / aspect_ratio))

                factor = (2 ** apply_to.index(model_chans)) if scale_depth else 1
                nh = random_divisor(h, latent_tile_size * factor, swap_size)
                nw = random_divisor(w, latent_tile_size * factor, swap_size)

                if nh * nw > 1:
                    q = rearrange(q, "b (nh h nw w) c -> (b nh nw) (h w) c", h=h // nh, w=w // nw, nh=nh, nw=nw)
                    self.temp = (nh, nw, h, w)
                return q, k, v

            return q, k, v
        def hypertile_out(out, extra_options):
            if self.temp is not None:
                nh, nw, h, w = self.temp
                self.temp = None
                out = rearrange(out, "(b nh nw) hw c -> b nh nw hw c", nh=nh, nw=nw)
                out = rearrange(out, "b nh nw (h w) c -> b (nh h nw w) c", h=h // nh, w=w // nw)
            return out


        m = model.clone()
        m.set_model_attn1_patch(hypertile_in)
        m.set_model_attn1_output_patch(hypertile_out)
        return (m, )

```
