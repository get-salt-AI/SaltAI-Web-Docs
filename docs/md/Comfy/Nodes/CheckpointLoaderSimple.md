# Load Checkpoint
## Documentation
- Class name: `CheckpointLoaderSimple`
- Category: `loaders`
- Output node: `False`

The `CheckpointLoaderSimple` node is designed to load model checkpoints without requiring a configuration name. It simplifies the checkpoint loading process by guessing the configuration based on the checkpoint name, making it easier to use for quick experiments or when the configuration is implicit.
## Input types
### Required
- **`ckpt_name`**
    - Specifies the name of the checkpoint to load. This parameter is crucial as it determines which checkpoint file will be used for loading the model, CLIP, and VAE components.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - Returns the loaded model component from the specified checkpoint.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Returns the loaded CLIP component from the specified checkpoint.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Returns the loaded VAE component from the specified checkpoint.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `CLIPTextEncode,VAEDecode,LoraLoader,CLIPSetLastLayer,Reroute,CR Apply LoRA Stack,VAEEncode,KSampler,UltimateSDUpscale`


## Source code
```python
class CheckpointLoaderSimple:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "ckpt_name": (folder_paths.get_filename_list("checkpoints"), ),
                             }}
    RETURN_TYPES = ("MODEL", "CLIP", "VAE")
    FUNCTION = "load_checkpoint"

    CATEGORY = "loaders"

    def load_checkpoint(self, ckpt_name, output_vae=True, output_clip=True):
        ckpt_path = folder_paths.get_full_path("checkpoints", ckpt_name)
        out = comfy.sd.load_checkpoint_guess_config(ckpt_path, output_vae=True, output_clip=True, embedding_directory=folder_paths.get_folder_paths("embeddings"))
        return out[:3]

```
