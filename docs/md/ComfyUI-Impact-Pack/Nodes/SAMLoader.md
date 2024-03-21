# SAMLoader (Impact)
## Documentation
- Class name: `SAMLoader`
- Category: `ImpactPack`
- Output node: `False`

The `SAMLoader` node is responsible for loading a SAM (Self-Attention Model) based on the model name and device mode specified. It filters out high-quality models, selects the appropriate model variant (vit_h, vit_l, vit_b), and ensures the model is loaded onto the preferred computational device (GPU or CPU). This process is crucial for initializing the model correctly for subsequent operations.
## Input types
### Required
- **`model_name`**
    - Specifies the name of the SAM model to be loaded. The selection excludes high-quality models and is crucial for determining the specific variant of the model to be used.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`device_mode`**
    - Determines the computational device (AUTO, Prefer GPU, CPU) for model execution. This choice affects performance and resource utilization, with GPU generally offering faster processing.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Output types
- **`sam_model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SAM_MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `FaceDetailer,ToDetailerPipe,SAMDetectorCombined,ToDetailerPipeSDXL,Reroute,ImpactSimpleDetectorSEGS,ImpactSimpleDetectorSEGS_for_AD,GroundingDinoSAMSegment (segment anything)`


## Source code
```python
class SAMLoader:
    @classmethod
    def INPUT_TYPES(cls):
        models = [x for x in folder_paths.get_filename_list("sams") if 'hq' not in x]
        return {
            "required": {
                "model_name": (models, ),
                "device_mode": (["AUTO", "Prefer GPU", "CPU"],),
            }
        }

    RETURN_TYPES = ("SAM_MODEL", )
    FUNCTION = "load_model"

    CATEGORY = "ImpactPack"

    def load_model(self, model_name, device_mode="auto"):
        modelname = folder_paths.get_full_path("sams", model_name)

        if 'vit_h' in model_name:
            model_kind = 'vit_h'
        elif 'vit_l' in model_name:
            model_kind = 'vit_l'
        else:
            model_kind = 'vit_b'

        sam = sam_model_registry[model_kind](checkpoint=modelname)
        size = os.path.getsize(modelname)
        sam.safe_to = core.SafeToGPU(size)

        # Unless user explicitly wants to use CPU, we use GPU
        device = comfy.model_management.get_torch_device() if device_mode == "Prefer GPU" else "CPU"

        if device_mode == "Prefer GPU":
            sam.safe_to.to_device(sam, device)

        sam.is_auto_mode = device_mode == "AUTO"

        print(f"Loads SAM model: {modelname} (device:{device_mode})")
        return (sam, )

```
