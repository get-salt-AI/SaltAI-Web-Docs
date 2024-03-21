# GLIGENLoader
## Documentation
- Class name: `GLIGENLoader`
- Category: `loaders`
- Output node: `False`

The GLIGENLoader node is responsible for loading a GLIGEN model from a specified path. It utilizes the `load_gligen` function to retrieve the model based on the given name, making it available for further processing or application within the system.
## Input types
### Required
- **`gligen_name`**
    - Specifies the name of the GLIGEN model to be loaded. This name is used to locate the model file within a predefined directory structure.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`gligen`**
    - The loaded GLIGEN model, ready for use in generating or manipulating images.
    - Python dtype: `Gligen`
    - Comfy dtype: `GLIGEN`
## Usage tips
- Infra type: `CPU`
- Common nodes: `GLIGENTextBoxApply`


## Source code
```python
class GLIGENLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "gligen_name": (folder_paths.get_filename_list("gligen"), )}}

    RETURN_TYPES = ("GLIGEN",)
    FUNCTION = "load_gligen"

    CATEGORY = "loaders"

    def load_gligen(self, gligen_name):
        gligen_path = folder_paths.get_full_path("gligen", gligen_name)
        gligen = comfy.sd.load_gligen(gligen_path)
        return (gligen,)

```
