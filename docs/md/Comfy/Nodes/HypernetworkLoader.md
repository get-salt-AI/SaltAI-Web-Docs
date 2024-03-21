# HypernetworkLoader
## Documentation
- Class name: `HypernetworkLoader`
- Category: `loaders`
- Output node: `False`

The `HypernetworkLoader` node is designed to load and apply a hypernetwork to a given model. It clones the original model, loads a hypernetwork patch based on the specified hypernetwork name and strength, and applies this patch to the attention layers of the cloned model. This process allows for dynamic modification of the model's behavior based on the hypernetwork, potentially enhancing its performance or adapting it to specific tasks.
## Input types
### Required
- **`model`**
    - The original model to which the hypernetwork will be applied. This model is cloned to ensure that the original remains unchanged.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`hypernetwork_name`**
    - The name of the hypernetwork to load. This name is used to retrieve the specific hypernetwork patch from a predefined directory.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`strength`**
    - A multiplier for the hypernetwork's effect on the model's attention layers, allowing for fine-tuning of the hypernetwork's impact.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The cloned model with the hypernetwork patch applied to its attention layers. This modified model is expected to exhibit altered behavior based on the hypernetwork.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class HypernetworkLoader:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "hypernetwork_name": (folder_paths.get_filename_list("hypernetworks"), ),
                              "strength": ("FLOAT", {"default": 1.0, "min": -10.0, "max": 10.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "load_hypernetwork"

    CATEGORY = "loaders"

    def load_hypernetwork(self, model, hypernetwork_name, strength):
        hypernetwork_path = folder_paths.get_full_path("hypernetworks", hypernetwork_name)
        model_hypernetwork = model.clone()
        patch = load_hypernetwork_patch(hypernetwork_path, strength)
        if patch is not None:
            model_hypernetwork.set_model_attn1_patch(patch)
            model_hypernetwork.set_model_attn2_patch(patch)
        return (model_hypernetwork,)

```
