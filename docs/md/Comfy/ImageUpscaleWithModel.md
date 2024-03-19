# Upscale Image (using Model)
## Documentation
- Class name: `ImageUpscaleWithModel`
- Category: `image/upscaling`
- Output node: `False`

This node is designed for upscaling images using a specified upscale model. It dynamically adjusts the tiling to manage memory efficiently and avoid out-of-memory errors, ensuring the upscale process can handle images of various sizes.
## Input types
### Required
- **`upscale_model`**
    - The upscale model to be used for image upscaling. It's crucial for defining the upscaling algorithm and its parameters.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `UPSCALE_MODEL`
- **`image`**
    - The input image to be upscaled. This image is processed and upscaled according to the specified model.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Output types
- **`image`**
    - The upscaled image, processed according to the upscale model and the dynamic tiling strategy to manage memory usage.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `ImageScaleBy,ImageScale,SaveImage,VHS_VideoCombine,PreviewImage,Reroute,ImageScaleToTotalPixels,FaceDetailer,CR Image Output,ImageCASharpening+`


## Source code
```python
class ImageUpscaleWithModel:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "upscale_model": ("UPSCALE_MODEL",),
                              "image": ("IMAGE",),
                              }}
    RETURN_TYPES = ("IMAGE",)
    FUNCTION = "upscale"

    CATEGORY = "image/upscaling"

    def upscale(self, upscale_model, image):
        device = model_management.get_torch_device()
        upscale_model.to(device)
        in_img = image.movedim(-1,-3).to(device)
        free_memory = model_management.get_free_memory(device)

        tile = 512
        overlap = 32

        oom = True
        while oom:
            try:
                steps = in_img.shape[0] * comfy.utils.get_tiled_scale_steps(in_img.shape[3], in_img.shape[2], tile_x=tile, tile_y=tile, overlap=overlap)
                pbar = comfy.utils.ProgressBar(steps)
                s = comfy.utils.tiled_scale(in_img, lambda a: upscale_model(a), tile_x=tile, tile_y=tile, overlap=overlap, upscale_amount=upscale_model.scale, pbar=pbar)
                oom = False
            except model_management.OOM_EXCEPTION as e:
                tile //= 2
                if tile < 128:
                    raise e

        upscale_model.cpu()
        s = torch.clamp(s.movedim(-3,-1), min=0, max=1.0)
        return (s,)

```
