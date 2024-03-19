# SaveLatent
## Documentation
- Class name: `SaveLatent`
- Category: `_for_testing`
- Output node: `True`

The `SaveLatent` node is not explicitly detailed in the provided context. However, based on the naming convention and typical operations associated with saving latent representations, it can be inferred that this node likely involves storing latent vectors or images to disk. This process would involve specifying a filename or prefix and the latent data to be saved. The operation is crucial for persisting generated latent representations for future use or analysis.
## Input types
### Required
- **`samples`**
    - Represents the latent vectors or images to be saved. This is crucial for the operation as it specifies the data that needs to be persisted for future use.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `LATENT`
- **`filename_prefix`**
    - Specifies the prefix for the filename under which the latent data will be saved. This allows for organized storage and easy retrieval of saved latents.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
The node doesn't have output types
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class SaveLatent:
    def __init__(self):
        self.output_dir = folder_paths.get_output_directory()

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "samples": ("LATENT", ),
                              "filename_prefix": ("STRING", {"default": "latents/ComfyUI"})},
                "hidden": {"prompt": "PROMPT", "extra_pnginfo": "EXTRA_PNGINFO"},
                }
    RETURN_TYPES = ()
    FUNCTION = "save"

    OUTPUT_NODE = True

    CATEGORY = "_for_testing"

    def save(self, samples, filename_prefix="ComfyUI", prompt=None, extra_pnginfo=None):
        full_output_folder, filename, counter, subfolder, filename_prefix = folder_paths.get_save_image_path(filename_prefix, self.output_dir)

        # support save metadata for latent sharing
        prompt_info = ""
        if prompt is not None:
            prompt_info = json.dumps(prompt)

        metadata = None
        if not args.disable_metadata:
            metadata = {"prompt": prompt_info}
            if extra_pnginfo is not None:
                for x in extra_pnginfo:
                    metadata[x] = json.dumps(extra_pnginfo[x])

        file = f"{filename}_{counter:05}_.latent"

        results = list()
        results.append({
            "filename": file,
            "subfolder": subfolder,
            "type": "output"
        })

        file = os.path.join(full_output_folder, file)

        output = {}
        output["latent_tensor"] = samples["samples"]
        output["latent_format_version_0"] = torch.tensor([])

        comfy.utils.save_torch_file(output, file, metadata=metadata)
        return { "ui": { "latents": results } }

```
