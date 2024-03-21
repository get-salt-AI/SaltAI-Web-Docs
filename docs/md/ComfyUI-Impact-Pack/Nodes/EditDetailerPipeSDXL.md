# Edit DetailerPipe (SDXL)
## Documentation
- Class name: `EditDetailerPipeSDXL`
- Category: `ImpactPack/Pipe`
- Output node: `False`

EditDetailerPipeSDXL extends the functionality of EditDetailerPipe by introducing additional input parameters that allow for more detailed and specific modifications to the detailer pipe. It enables the inclusion of LoRA and Wildcard elements to enhance the text, alongside a comprehensive set of tools for refining and conditioning the output.
## Input types
### Required
- **`detailer_pipe`**
    - The base detailer pipe to be edited or enhanced. It serves as the foundation for further modifications and enhancements.
    - Python dtype: `DETAILER_PIPE`
    - Comfy dtype: `DETAILER_PIPE`
- **`wildcard`**
    - A multiline string input that allows for dynamic text inputs, enabling users to add custom text or commands that can influence the detailer pipe's behavior.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - Allows the user to select a LoRA (Low-Rank Adaptation) from a list to add to the text, enhancing the model's adaptability and performance.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`Select to add Wildcard`**
    - Enables the selection of predefined wildcard elements to be added to the text, offering more customization options.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`model`**
    - The model to be used in the detailer pipe, providing the core functionality.
    - Python dtype: `MODEL`
    - Comfy dtype: `MODEL`
- **`clip`**
    - A CLIP model used for image and text processing within the detailer pipe.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`vae`**
    - A Variational Autoencoder (VAE) model used for generating and manipulating images.
    - Python dtype: `VAE`
    - Comfy dtype: `VAE`
- **`positive`**
    - Positive conditioning terms to guide the model's output towards desired attributes.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Negative conditioning terms to steer the model's output away from undesired attributes.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
- **`refiner_model`**
    - An optional model used for refining the outputs further.
    - Python dtype: `MODEL`
    - Comfy dtype: `MODEL`
- **`refiner_clip`**
    - An optional CLIP model used in conjunction with the refiner model for enhanced processing capabilities.
    - Python dtype: `CLIP`
    - Comfy dtype: `CLIP`
- **`refiner_positive`**
    - Positive conditioning terms for the refiner model, aiming to refine the outputs towards more desired attributes.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
- **`refiner_negative`**
    - Negative conditioning terms for the refiner model, used to avoid undesired attributes in the refined outputs.
    - Python dtype: `CONDITIONING`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - A bounding box detector used for identifying and processing specific areas within images.
    - Python dtype: `BBOX_DETECTOR`
    - Comfy dtype: `BBOX_DETECTOR`
- **`sam_model`**
    - A SAM model used for semantic adjustment of the media content.
    - Python dtype: `SAM_MODEL`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector`**
    - A segmentation detector used for detailed image segmentation tasks.
    - Python dtype: `SEGM_DETECTOR`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - A hook for integrating custom processing or modifications into the detailer pipe.
    - Python dtype: `DETAILER_HOOK`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_pipe`**
    - The modified detailer pipe, incorporating all specified enhancements and adjustments.
    - Python dtype: `DETAILER_PIPE`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class EditDetailerPipeSDXL(EditDetailerPipe):
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "detailer_pipe": ("DETAILER_PIPE",),
                "wildcard": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                "Select to add LoRA": (["Select the LoRA to add to the text"] + folder_paths.get_filename_list("loras"),),
                "Select to add Wildcard": (["Select the Wildcard to add to the text"],),
            },
            "optional": {
                "model": ("MODEL",),
                "clip": ("CLIP",),
                "vae": ("VAE",),
                "positive": ("CONDITIONING",),
                "negative": ("CONDITIONING",),
                "refiner_model": ("MODEL",),
                "refiner_clip": ("CLIP",),
                "refiner_positive": ("CONDITIONING",),
                "refiner_negative": ("CONDITIONING",),
                "bbox_detector": ("BBOX_DETECTOR",),
                "sam_model": ("SAM_MODEL",),
                "segm_detector": ("SEGM_DETECTOR",),
                "detailer_hook": ("DETAILER_HOOK",),
            },
        }

```
