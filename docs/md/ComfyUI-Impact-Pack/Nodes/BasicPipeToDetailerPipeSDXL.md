# BasicPipe -> DetailerPipe (SDXL)
## Documentation
- Class name: `BasicPipeToDetailerPipeSDXL`
- Category: `ImpactPack/Pipe`
- Output node: `False`

This node is designed to transform a basic pipe into a more detailed pipe specifically tailored for the SDXL model. It enriches the basic pipe with additional parameters and models to enhance its functionality and adapt it to the requirements of the SDXL model.
## Input types
### Required
- **`base_basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
- **`refiner_basic_pipe`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `BASIC_PIPE`
- **`bbox_detector`**
    - A bounding box detector model, crucial for identifying and working with specific regions within images.
    - Python dtype: `str`
    - Comfy dtype: `BBOX_DETECTOR`
- **`wildcard`**
    - A wildcard string input allowing for dynamic and flexible text inputs to further customize the pipe's behavior.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - Allows the selection of LoRA layers to be added to the text, enhancing the model's adaptability and performance on specific tasks.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`Select to add Wildcard`**
    - Enables the selection of predefined wildcard options to add to the text, offering additional customization for the generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`sam_model_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector_opt`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_pipe`**
    - The output is a detailed pipe tailored for the SDXL model, incorporating additional parameters and models to enhance its functionality.
    - Python dtype: `str`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class BasicPipeToDetailerPipeSDXL:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"base_basic_pipe": ("BASIC_PIPE",),
                             "refiner_basic_pipe": ("BASIC_PIPE",),
                             "bbox_detector": ("BBOX_DETECTOR", ),
                             "wildcard": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                             "Select to add LoRA": (["Select the LoRA to add to the text"] + folder_paths.get_filename_list("loras"),),
                             "Select to add Wildcard": (["Select the Wildcard to add to the text"],),
                             },
                "optional": {
                    "sam_model_opt": ("SAM_MODEL", ),
                    "segm_detector_opt": ("SEGM_DETECTOR",),
                    "detailer_hook": ("DETAILER_HOOK",),
                    },
                }

    RETURN_TYPES = ("DETAILER_PIPE", )
    RETURN_NAMES = ("detailer_pipe", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, *args, **kwargs):
        base_basic_pipe = kwargs['base_basic_pipe']
        refiner_basic_pipe = kwargs['refiner_basic_pipe']
        bbox_detector = kwargs['bbox_detector']
        wildcard = kwargs['wildcard']
        sam_model_opt = kwargs.get('sam_model_opt', None)
        segm_detector_opt = kwargs.get('segm_detector_opt', None)
        detailer_hook = kwargs.get('detailer_hook', None)

        model, clip, vae, positive, negative = base_basic_pipe
        refiner_model, refiner_clip, refiner_vae, refiner_positive, refiner_negative = refiner_basic_pipe
        pipe = model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, refiner_model, refiner_clip, refiner_positive, refiner_negative
        return (pipe, )

```
