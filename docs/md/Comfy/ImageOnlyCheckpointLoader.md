# Image Only Checkpoint Loader (img2vid model)
## Documentation
- Class name: `ImageOnlyCheckpointLoader`
- Category: `loaders/video_models`
- Output node: `False`

This node is designed to load checkpoints specifically for image-based models in video generation tasks. It selectively loads the model, CLIP vision, and VAE components from a given checkpoint, allowing for a streamlined setup in scenarios where only image processing capabilities are required.
## Input types
### Required
- **`ckpt_name`**
    - Specifies the name of the checkpoint to load. This parameter is crucial for identifying the correct file from which to load the model, CLIP vision, and VAE components.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - The main model loaded from the checkpoint, typically used for generating or manipulating images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip_vision`**
    - The CLIP vision component loaded from the checkpoint, useful for tasks involving image understanding and manipulation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
- **`vae`**
    - The VAE component loaded from the checkpoint, often used for encoding and decoding images in generative tasks.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `SVD_img2vid_Conditioning,VAEDecode,VideoLinearCFGGuidance,Reroute,StableZero123_Conditioning,LoraLoader,Anything Everywhere,KSampler,workflow/front`


## Source code
```python
class ImageOnlyCheckpointLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "ckpt_name": (folder_paths.get_filename_list("checkpoints"), ),
                             }}
    RETURN_TYPES = ("MODEL", "CLIP_VISION", "VAE")
    FUNCTION = "load_checkpoint"

    CATEGORY = "loaders/video_models"

    def load_checkpoint(self, ckpt_name, output_vae=True, output_clip=True):
        ckpt_path = folder_paths.get_full_path("checkpoints", ckpt_name)
        out = comfy.sd.load_checkpoint_guess_config(ckpt_path, output_vae=True, output_clip=False, output_clipvision=True, embedding_directory=folder_paths.get_folder_paths("embeddings"))
        return (out[0], out[3], out[2])

```
