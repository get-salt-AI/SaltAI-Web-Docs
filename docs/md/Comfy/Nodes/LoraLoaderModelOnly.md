# LoraLoaderModelOnly
## Documentation
- Class name: `LoraLoaderModelOnly`
- Category: `loaders`
- Output node: `False`

This node is specialized in loading a LoRA model with a specified strength for the model only, excluding any CLIP modifications. It leverages the underlying LoRA loading mechanism to apply the LoRA adjustments to the provided model based on the specified LoRA name and strength, enhancing or altering the model's behavior without affecting any associated CLIP model.
## Input types
### Required
- **`model`**
    - The model to which the LoRA adjustments will be applied. It's crucial for defining the base model that will be enhanced or modified by the LoRA parameters.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`lora_name`**
    - The name of the LoRA file to be loaded. This determines which specific LoRA adjustments are applied to the model, impacting the model's behavior and performance.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`strength_model`**
    - Defines the intensity of the LoRA adjustments applied to the model. This parameter allows for fine-tuning the effect of LoRA on the model, enabling customization of the model's behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The model with LoRA adjustments applied. This output represents the enhanced or modified version of the input model, reflecting the specified LoRA adjustments.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `IPAdapterApplyFaceID,LoraLoaderModelOnly,KSampler,FreeU_V2,UltimateSDUpscale`


## Source code
```python
class LoraLoaderModelOnly(LoraLoader):
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "lora_name": (folder_paths.get_filename_list("loras"), ),
                              "strength_model": ("FLOAT", {"default": 1.0, "min": -20.0, "max": 20.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL",)
    FUNCTION = "load_lora_model_only"

    def load_lora_model_only(self, model, lora_name, strength_model):
        return (self.load_lora(model, None, lora_name, strength_model, 0)[0],)

```
