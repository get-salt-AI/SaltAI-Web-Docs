# LoadLatent
## Documentation
- Class name: `LoadLatent`
- Category: `_for_testing`
- Output node: `False`

This node is responsible for loading latent representations from files with a '.latent' extension located in a specified directory. It adjusts the scale of the latent tensor based on its version.
## Input types
### Required
- **`latent`**
    - Specifies the name of the latent file to be loaded. The file must exist in the pre-defined input directory and have a '.latent' extension.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`latent`**
    - Returns the loaded and potentially rescaled latent tensor as a dictionary with the key 'samples'.
    - Python dtype: `Dict[str, torch.Tensor]`
    - Comfy dtype: `LATENT`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LoadLatent:
    @classmethod
    def INPUT_TYPES(s):
        input_dir = folder_paths.get_input_directory()
        files = [f for f in os.listdir(input_dir) if os.path.isfile(os.path.join(input_dir, f)) and f.endswith(".latent")]
        return {"required": {"latent": [sorted(files), ]}, }

    CATEGORY = "_for_testing"

    RETURN_TYPES = ("LATENT", )
    FUNCTION = "load"

    def load(self, latent):
        latent_path = folder_paths.get_annotated_filepath(latent)
        latent = safetensors.torch.load_file(latent_path, device="cpu")
        multiplier = 1.0
        if "latent_format_version_0" not in latent:
            multiplier = 1.0 / 0.18215
        samples = {"samples": latent["latent_tensor"].float() * multiplier}
        return (samples, )

    @classmethod
    def IS_CHANGED(s, latent):
        image_path = folder_paths.get_annotated_filepath(latent)
        m = hashlib.sha256()
        with open(image_path, 'rb') as f:
            m.update(f.read())
        return m.digest().hex()

    @classmethod
    def VALIDATE_INPUTS(s, latent):
        if not folder_paths.exists_annotated_filepath(latent):
            return "Invalid latent file: {}".format(latent)
        return True

```
