# Load Checkpoint With Config (DEPRECATED)
## Documentation
- Class name: `CheckpointLoader`
- Category: `advanced/loaders`
- Output node: `False`

The `CheckpointLoader` node is designed to load model checkpoints along with optional VAE and CLIP components based on specified configuration and checkpoint names. It facilitates the initialization of models for further operations or inference by loading the necessary components from disk.
## Input types
### Required
- **`config_name`**
    - Specifies the name of the configuration file to use, which is essential for determining the model's architecture and parameters for initialization.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`ckpt_name`**
    - Indicates the specific checkpoint file name to load, which contains the trained model weights necessary for the model's operation.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - Returns the main model loaded from the checkpoint.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Optionally returns the CLIP component if available and requested.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Optionally returns the VAE component if available and requested.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class CheckpointLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "config_name": (folder_paths.get_filename_list("configs"), ),
                              "ckpt_name": (folder_paths.get_filename_list("checkpoints"), )}}
    RETURN_TYPES = ("MODEL", "CLIP", "VAE")
    FUNCTION = "load_checkpoint"

    CATEGORY = "advanced/loaders"

    def load_checkpoint(self, config_name, ckpt_name, output_vae=True, output_clip=True):
        config_path = folder_paths.get_full_path("configs", config_name)
        ckpt_path = folder_paths.get_full_path("checkpoints", ckpt_name)
        return comfy.sd.load_checkpoint(config_path, ckpt_path, output_vae=True, output_clip=True, embedding_directory=folder_paths.get_folder_paths("embeddings"))

```
