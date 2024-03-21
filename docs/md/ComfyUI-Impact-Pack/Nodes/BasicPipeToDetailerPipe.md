# BasicPipe -> DetailerPipe
## Documentation
- Class name: `BasicPipeToDetailerPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

This node is not explicitly detailed in the provided context, suggesting it may serve as a foundational or utility component within the Impact Pack's pipe module. Its role could involve transforming or preparing basic pipeline configurations for further processing or detailing, aligning with the naming convention of other classes in the module.
## Input types
### Required
- **`basic_pipe`**
    - Represents the basic pipeline configuration to be transformed or detailed. It is essential for the node's operation as it serves as the input from which further processing or detailing is derived.
    - Python dtype: `Tuple[torch.nn.Module, torch.nn.Module, torch.nn.Module, str, str]`
    - Comfy dtype: `BASIC_PIPE`
- **`bbox_detector`**
    - Specifies the bounding box detector component to be included in the detailer pipe. It plays a crucial role in object detection within images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `BBOX_DETECTOR`
- **`wildcard`**
    - A string parameter that allows for dynamic input, potentially influencing the behavior or output of the node in a flexible manner.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - Allows the user to select a LoRA (Locally Optimized Receptive Attention) to add to the text, enhancing the detailer pipe's capabilities.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`Select to add Wildcard`**
    - Enables the user to select a wildcard option to add to the text, providing additional flexibility in the detailer pipe's configuration.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`sam_model_opt`**
    - An optional SAM model that can be included in the detailer pipe for enhanced semantic understanding or image processing.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector_opt`**
    - An optional segmentation detector that can be included to enhance the detailer pipe's ability to understand and process image segments.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - An optional hook that allows for custom processing or modifications within the detailer pipe, providing a mechanism for customization.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_pipe`**
    - The output detailer pipe configuration, enriched with the specified inputs and potentially additional components for enhanced processing capabilities.
    - Python dtype: `Tuple[torch.nn.Module, torch.nn.Module, torch.nn.Module, str, str, str, torch.nn.Module, Optional[torch.nn.Module], Optional[torch.nn.Module], Optional[torch.nn.Module], NoneType, NoneType, NoneType, NoneType]`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class BasicPipeToDetailerPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"basic_pipe": ("BASIC_PIPE",),
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
        basic_pipe = kwargs['basic_pipe']
        bbox_detector = kwargs['bbox_detector']
        wildcard = kwargs['wildcard']
        sam_model_opt = kwargs.get('sam_model_opt', None)
        segm_detector_opt = kwargs.get('segm_detector_opt', None)
        detailer_hook = kwargs.get('detailer_hook', None)

        model, clip, vae, positive, negative = basic_pipe
        pipe = model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, None, None, None, None
        return (pipe, )

```
