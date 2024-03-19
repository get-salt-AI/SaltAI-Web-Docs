# ImageOnlyCheckpointSave
## Documentation
- Class name: `ImageOnlyCheckpointSave`
- Category: `_for_testing`
- Output node: `True`

This node specializes in saving model checkpoints specifically for image-based models. It extends the functionality of a general checkpoint saving mechanism to include additional components like CLIP vision and VAE models, tailored for image processing tasks. The node allows for customization of the saved checkpoint's filename and supports optional inclusion of a prompt and extra PNG information.
## Input types
### Required
- **`model`**
    - The primary model whose state is to be saved. This is central to the checkpoint saving process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip_vision`**
    - A CLIP vision model component to be included in the checkpoint. This is important for tasks that involve understanding or generating images in the context of natural language descriptions.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
- **`vae`**
    - A VAE (Variational Autoencoder) model component to be included in the checkpoint. VAEs are often used in image processing for generating new images or modifying existing ones.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`filename_prefix`**
    - The prefix for the filename under which the checkpoint will be saved. This allows for organizing and identifying checkpoints easily.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class ImageOnlyCheckpointSave(comfy_extras.nodes_model_merging.CheckpointSave):
    CATEGORY = "_for_testing"

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "clip_vision": ("CLIP_VISION",),
                              "vae": ("VAE",),
                              "filename_prefix": ("STRING", {"default": "checkpoints/ComfyUI"}),},
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},}

    def save(self, model, clip_vision, vae, filename_prefix, prompt=None, extra_pnginfo=None):
        comfy_extras.nodes_model_merging.save_checkpoint(model, clip_vision=clip_vision, vae=vae, filename_prefix=filename_prefix, output_dir=self.output_dir, prompt=prompt, extra_pnginfo=extra_pnginfo)
        return {}

```
