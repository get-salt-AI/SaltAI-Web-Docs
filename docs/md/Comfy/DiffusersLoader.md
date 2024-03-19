# DiffusersLoader
## Documentation
- Class name: `DiffusersLoader`
- Category: `advanced/loaders/deprecated`
- Output node: `False`

The `DiffusersLoader` node is responsible for loading models related to the diffusers library, specifically handling the loading of diffusion models, CLIP models, and VAEs based on the provided model path. It supports conditional loading of VAE and CLIP models based on the input flags.
## Input types
### Required
- **`model_path`**
    - The path to the model directory. This path is crucial for locating the specific models to be loaded. The correct identification and loading of the diffusion model, CLIP model, and VAE depend on this path, directly influencing the node's execution and the types of models available for subsequent operations.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`model`**
    - Returns the loaded diffusion model.
    - Python dtype: `comfy.sd.DiffusionModel`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Returns the loaded CLIP model, if requested.
    - Python dtype: `Optional[comfy.sd.CLIPModel]`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Returns the loaded VAE model, if requested.
    - Python dtype: `Optional[comfy.sd.VAEModel]`
    - Comfy dtype: `VAE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class DiffusersLoader:
    @classmethod
    def INPUT_TYPES(cls):
        paths = []
        for search_path in folder_paths.get_folder_paths("diffusers"):
            if os.path.exists(search_path):
                for root, subdir, files in os.walk(search_path, followlinks=True):
                    if "model_index.json" in files:
                        paths.append(os.path.relpath(root, start=search_path))

        return {"required": {"model_path": (paths,), }}
    RETURN_TYPES = ("MODEL", "CLIP", "VAE")
    FUNCTION = "load_checkpoint"

    CATEGORY = "advanced/loaders/deprecated"

    def load_checkpoint(self, model_path, output_vae=True, output_clip=True):
        for search_path in folder_paths.get_folder_paths("diffusers"):
            if os.path.exists(search_path):
                path = os.path.join(search_path, model_path)
                if os.path.exists(path):
                    model_path = path
                    break

        return comfy.diffusers_load.load_diffusers(model_path, output_vae=output_vae, output_clip=output_clip, embedding_directory=folder_paths.get_folder_paths("embeddings"))

```
