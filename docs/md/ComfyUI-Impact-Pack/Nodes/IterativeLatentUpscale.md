# Iterative Upscale (Latent/on Pixel Space)
## Documentation
- Class name: `IterativeLatentUpscale`
- Category: `ImpactPack/Upscale`
- Output node: `False`

This node performs iterative upscaling of latent images over a specified number of steps, gradually increasing the scale factor until the desired upscale factor is reached. It utilizes an upscaler to adjust the dimensions of the latent images at each step, providing a detailed and controlled approach to image upscaling.
## Input types
### Required
- **`samples`**
    - The latent representation of images to be upscaled. It serves as the starting point for the iterative upscaling process.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`upscale_factor`**
    - The final scale factor to achieve after all iterative steps. It determines the overall increase in size of the latent images.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`steps`**
    - The number of iterative steps to perform the upscaling. Each step incrementally increases the size of the images, contributing to a gradual upscale.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`temp_prefix`**
    - An optional prefix for temporary files generated during the upscaling process. It aids in organizing and identifying intermediate results.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `STRING`
- **`upscaler`**
    - The upscaling method or model to be used for resizing the images at each iterative step. It plays a crucial role in determining the quality and characteristics of the upscaled images.
    - Python dtype: `Any`
    - Comfy dtype: `UPSCALER`
## Output types
- **`latent`**
    - The final upscaled latent representation of the images after completing all iterative steps.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
- **`vae`**
    - The variational autoencoder used in the upscaling process. It is involved in encoding and decoding the images during the upscaling.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `VAEDecode`


## Source code
```python
class IterativeLatentUpscale:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "samples": ("LATENT", ),
                     "upscale_factor": ("FLOAT", {"default": 1.5, "min": 1, "max": 10000, "step": 0.1}),
                     "steps": ("INT", {"default": 3, "min": 1, "max": 10000, "step": 1}),
                     "temp_prefix": ("STRING", {"default": ""}),
                     "upscaler": ("UPSCALER",)
                },
                "hidden": {"unique_id": "UNIQUE_ID"},
        }

    RETURN_TYPES = ("LATENT", "VAE")
    RETURN_NAMES = ("latent", "vae")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Upscale"

    def doit(self, samples, upscale_factor, steps, temp_prefix, upscaler, unique_id):
        w = samples['samples'].shape[3]*8  # image width
        h = samples['samples'].shape[2]*8  # image height

        if temp_prefix == "":
            temp_prefix = None

        upscale_factor_unit = max(0, (upscale_factor-1.0)/steps)
        current_latent = samples
        scale = 1

        for i in range(steps-1):
            scale += upscale_factor_unit
            new_w = w*scale
            new_h = h*scale
            core.update_node_status(unique_id, f"{i+1}/{steps} steps | x{scale:.2f}", (i+1)/steps)
            print(f"IterativeLatentUpscale[{i+1}/{steps}]: {new_w:.1f}x{new_h:.1f} (scale:{scale:.2f}) ")
            step_info = i, steps
            current_latent = upscaler.upscale_shape(step_info, current_latent, new_w, new_h, temp_prefix)

        if scale < upscale_factor:
            new_w = w*upscale_factor
            new_h = h*upscale_factor
            core.update_node_status(unique_id, f"Final step | x{upscale_factor:.2f}", 1.0)
            print(f"IterativeLatentUpscale[Final]: {new_w:.1f}x{new_h:.1f} (scale:{upscale_factor:.2f}) ")
            step_info = steps, steps
            current_latent = upscaler.upscale_shape(step_info, current_latent, new_w, new_h, temp_prefix)

        core.update_node_status(unique_id, "", None)

        return (current_latent, upscaler.vae)

```
