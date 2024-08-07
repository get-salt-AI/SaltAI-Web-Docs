---
tags:
- DepthMap
- Image
- Inpaint
---

# Inpaint (using Model)
## Documentation
- Class name: `INPAINT_InpaintWithModel`
- Category: `inpaint`
- Output node: `False`

This node is designed to perform inpainting on images using a specified inpainting model. It takes an image and a mask as inputs, along with the inpainting model, and applies the model to the specified areas of the image to fill in or correct missing or undesired parts. The node supports optional upscaling of the inpainted image for enhanced detail and resolution.
## Input types
### Required
- **`inpaint_model`**
    - The inpainting model to be used for the inpainting process. This model dictates the inpainting technique and its underlying architecture, affecting the quality and style of the inpainting results.
    - Comfy dtype: `INPAINT_MODEL`
    - Python dtype: `mat.MAT | Any`
- **`image`**
    - The image to be inpainted, provided as a tensor. This is the target image where the inpainting will be applied.
    - Comfy dtype: `IMAGE`
    - Python dtype: `torch.Tensor`
- **`mask`**
    - A tensor representing the mask that indicates the areas of the image to be inpainted. The mask guides the inpainting process by specifying which parts of the image need correction or filling.
    - Comfy dtype: `MASK`
    - Python dtype: `torch.Tensor`
- **`seed`**
    - An integer seed for random number generation, ensuring reproducibility of the inpainting results.
    - Comfy dtype: `INT`
    - Python dtype: `int`
### Optional
- **`optional_upscale_model`**
    - An optional model for upscaling the inpainted image. This allows for enhancing the resolution and detail of the inpainted areas, improving the overall quality of the output.
    - Comfy dtype: `UPSCALE_MODEL`
    - Python dtype: `Any`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The inpainted image, returned as a tensor. This output represents the final result of the inpainting process, with the specified areas corrected or filled in.
    - Python dtype: `torch.Tensor`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class InpaintWithModel:
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "inpaint_model": ("INPAINT_MODEL",),
                "image": ("IMAGE",),
                "mask": ("MASK",),
                "seed": ("INT", {"default": 0, "min": 0, "max": 0xFFFFFFFFFFFFFFFF}),
            },
            "optional": {
                "optional_upscale_model": ("UPSCALE_MODEL",),
            },
        }

    RETURN_TYPES = ("IMAGE",)
    CATEGORY = "inpaint"
    FUNCTION = "inpaint"

    def inpaint(
        self,
        inpaint_model: mat.MAT | Any,
        image: Tensor,
        mask: Tensor,
        seed: int,
        optional_upscale_model=None,
    ):
        if isinstance(inpaint_model, mat.MAT):
            required_size = 512
        elif inpaint_model.architecture.id == "LaMa":
            required_size = 256
        else:
            raise ValueError(f"Unknown model_arch {type(inpaint_model)}")

        if optional_upscale_model != None:
            from comfy_extras.nodes_upscale_model import ImageUpscaleWithModel

            upscaler = ImageUpscaleWithModel

        image, mask = to_torch(image, mask)
        batch_size = image.shape[0]
        if mask.shape[0] != batch_size:
            mask = mask[0].unsqueeze(0).repeat(batch_size, 1, 1, 1)

        image_device = image.device
        device = get_torch_device()
        inpaint_model.to(device)
        batch_image = []
        pbar = ProgressBar(batch_size)

        for i in trange(batch_size):
            work_image, work_mask = image[i].unsqueeze(0), mask[i].unsqueeze(0)
            work_image, work_mask, original_size = resize_square(
                work_image, work_mask, required_size
            )
            work_mask = mask_floor(work_mask)

            torch.manual_seed(seed)
            work_image = inpaint_model(work_image.to(device), work_mask.to(device))

            if optional_upscale_model != None:
                work_image = work_image.movedim(1, -1)
                work_image = upscaler.upscale(upscaler, optional_upscale_model, work_image)
                work_image = work_image[0].movedim(-1, 1)

            work_image.to(image_device)
            work_image = undo_resize_square(work_image.to(image_device), original_size)
            work_image = image[i] + (work_image - image[i]) * mask_floor(mask[i])

            batch_image.append(work_image)
            pbar.update(1)

        inpaint_model.cpu()
        result = torch.cat(batch_image, dim=0)
        return (to_comfy(result),)

```
