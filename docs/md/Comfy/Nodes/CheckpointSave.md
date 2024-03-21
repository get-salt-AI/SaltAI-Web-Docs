# CheckpointSave
## Documentation
- Class name: `CheckpointSave`
- Category: `advanced/model_merging`
- Output node: `True`

The `CheckpointSave` node is designed for saving models, CLIP, and VAE checkpoints to a specified directory. It allows for the inclusion of additional information such as prompts and extra PNG metadata.
## Input types
### Required
- **`model`**
    - The model to be saved. It is crucial for preserving the trained state of the model for future use or further training.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP model to be saved alongside the main model. Essential for tasks that require text-image alignment.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE model to be saved. Important for generative tasks, especially in the context of image synthesis.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
- **`filename_prefix`**
    - The prefix for the filename under which the checkpoint will be saved. Allows for organized storage and easy retrieval.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CheckpointSave:
    def __init__(self):
        self.output_dir = folder_paths.get_output_directory()

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "clip": ("CLIP",),
                              "vae": ("VAE",),
                              "filename_prefix": ("STRING", {"default": "checkpoints/ComfyUI"}),},
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},}
    RETURN_TYPES = ()
    FUNCTION = "save"
    OUTPUT_NODE = True

    CATEGORY = "advanced/model_merging"

    def save(self, model, clip, vae, filename_prefix, prompt=None, extra_pnginfo=None):
        save_checkpoint(model, clip=clip, vae=vae, filename_prefix=filename_prefix, output_dir=self.output_dir, prompt=prompt, extra_pnginfo=extra_pnginfo)
        return {}

```
