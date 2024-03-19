# Load LoRA
## Documentation
- Class name: `LoraLoader`
- Category: `loaders`
- Output node: `False`

The LoraLoader node is designed to load and apply LoRA (Low-Rank Adaptation) adjustments to given models and CLIP instances. It dynamically adjusts the strength of the LoRA modifications based on provided parameters, allowing for fine-tuned control over the adaptation process. This node supports conditional loading to avoid redundant operations if the same LoRA configuration has been loaded previously.
## Input types
### Required
- **`model`**
    - The model parameter represents the neural network model to which LoRA adjustments will be applied. It's crucial for adapting the model's behavior without extensive retraining.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The clip parameter signifies the CLIP model instance that will undergo LoRA adjustments. This is essential for modifying CLIP's behavior in a controlled manner.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`lora_name`**
    - Specifies the name of the LoRA configuration to load. This parameter is key to identifying the specific LoRA adjustments to apply.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`strength_model`**
    - Determines the intensity of LoRA adjustments applied to the model. This allows for precise control over the adaptation's impact.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`strength_clip`**
    - Sets the intensity of LoRA adjustments for the CLIP model, enabling fine-tuned control over its adaptation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`model`**
    - The modified model with LoRA adjustments applied.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP model instance after LoRA adjustments.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
## Usage tips
- Infra type: `GPU`
- Common nodes: `LoraLoader,CLIPTextEncode,Reroute,VideoLinearCFGGuidance,KSampler,FaceDetailer,ModelSamplingDiscrete,ADE_AnimateDiffLoaderWithContext,KSampler //Inspire,ToBasicPipe`


## Source code
```python
class LoraLoader:
    def __init__(self):
        self.loaded_lora = None

    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "model": ("MODEL",),
                              "clip": ("CLIP", ),
                              "lora_name": (folder_paths.get_filename_list("loras"), ),
                              "strength_model": ("FLOAT", {"default": 1.0, "min": -20.0, "max": 20.0, "step": 0.01}),
                              "strength_clip": ("FLOAT", {"default": 1.0, "min": -20.0, "max": 20.0, "step": 0.01}),
                              }}
    RETURN_TYPES = ("MODEL", "CLIP")
    FUNCTION = "load_lora"

    CATEGORY = "loaders"

    def load_lora(self, model, clip, lora_name, strength_model, strength_clip):
        if strength_model == 0 and strength_clip == 0:
            return (model, clip)

        lora_path = folder_paths.get_full_path("loras", lora_name)
        lora = None
        if self.loaded_lora is not None:
            if self.loaded_lora[0] == lora_path:
                lora = self.loaded_lora[1]
            else:
                temp = self.loaded_lora
                self.loaded_lora = None
                del temp

        if lora is None:
            lora = comfy.utils.load_torch_file(lora_path, safe_load=True)
            self.loaded_lora = (lora_path, lora)

        model_lora, clip_lora = comfy.sd.load_lora_for_models(model, clip, lora, strength_model, strength_clip)
        return (model_lora, clip_lora)

```
