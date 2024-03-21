# unCLIPCheckpointLoader
## Documentation
- Class name: `unCLIPCheckpointLoader`
- Category: `loaders`
- Output node: `False`

This node is responsible for loading checkpoints specifically designed for the unCLIP model. It retrieves the model's checkpoint from a specified path and configures it for use, including loading various components such as VAE, CLIP, and CLIP Vision models.
## Input types
### Required
- **`ckpt_name`**
    - Specifies the name of the checkpoint to be loaded. This is crucial for identifying the correct file within the 'checkpoints' directory and loading the associated model.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - Returns the main model loaded from the checkpoint.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Returns the CLIP model component loaded from the checkpoint.
    - Python dtype: `comfy.sd.CLIP`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Returns the VAE model component loaded from the checkpoint.
    - Python dtype: `comfy.sd.VAE`
    - Comfy dtype: `VAE`
- **`clip_vision`**
    - Returns the CLIP Vision model component loaded from the checkpoint.
    - Python dtype: `comfy.sd.CLIPVision`
    - Comfy dtype: `CLIP_VISION`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class unCLIPCheckpointLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "ckpt_name": (folder_paths.get_filename_list("checkpoints"), ),
                             }}
    RETURN_TYPES = ("MODEL", "CLIP", "VAE", "CLIP_VISION")
    FUNCTION = "load_checkpoint"

    CATEGORY = "loaders"

    def load_checkpoint(self, ckpt_name, output_vae=True, output_clip=True):
        ckpt_path = folder_paths.get_full_path("checkpoints", ckpt_name)
        out = comfy.sd.load_checkpoint_guess_config(ckpt_path, output_vae=True, output_clip=True, output_clipvision=True, embedding_directory=folder_paths.get_folder_paths("embeddings"))
        return out

```
