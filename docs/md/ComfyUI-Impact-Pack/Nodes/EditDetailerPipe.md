# Edit DetailerPipe
## Documentation
- Class name: `EditDetailerPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The EditDetailerPipe node is designed to enhance and detail the content of a given input through a series of transformations and additions. It allows for the customization of text and images by applying specific detailer pipes, wildcards, LoRAs, and other optional parameters like models and detectors to refine the output further.
## Input types
### Required
- **`detailer_pipe`**
    - Represents the core transformation pipeline to which additional details and enhancements are applied. It is crucial for defining the base content and structure that will be further detailed.
    - Python dtype: `Tuple[DETAILER_PIPE]`
    - Comfy dtype: `DETAILER_PIPE`
- **`wildcard`**
    - Allows for the insertion of dynamic, user-defined text into the content, enhancing its customization and relevance.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - Enables the selection and addition of specific LoRA (Locally Optimized Re-Attention) mechanisms to the text, offering a way to fine-tune and enhance the content's impact.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`Select to add Wildcard`**
    - Provides options for incorporating predefined wildcards into the content, further customizing and enriching the output.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`model`**
    - Specifies the model used for generating or refining the content, contributing to the overall quality and specificity of the output.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Defines the CLIP model used for guiding the content generation towards specific visual concepts or themes.
    - Python dtype: `str`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Specifies the VAE (Variational Autoencoder) model used for generating or refining the content, enhancing its diversity and creativity.
    - Python dtype: `str`
    - Comfy dtype: `VAE`
- **`positive`**
    - Sets positive conditioning factors that guide the content generation towards desired attributes or themes.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Sets negative conditioning factors that steer the content generation away from certain attributes or themes, refining the output.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - Specifies a bounding box detector used for identifying and processing specific areas within images, enhancing the content's focus and relevance.
    - Python dtype: `str`
    - Comfy dtype: `BBOX_DETECTOR`
- **`sam_model`**
    - Specifies a SAM model used for semantic adjustment of the content, improving its coherence and relevance.
    - Python dtype: `str`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector`**
    - Specifies a segmentation detector used for identifying and processing specific segments within images, enhancing the content's detail and relevance.
    - Python dtype: `str`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - Provides a mechanism for applying custom transformations or enhancements to the content, allowing for further customization and refinement.
    - Python dtype: `str`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_pipe`**
    - Outputs the enhanced and detailed content as a result of the applied transformations and additions.
    - Python dtype: `Tuple[DETAILER_PIPE]`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FaceDetailerPipe`


## Source code
```python
class EditDetailerPipe:
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
                "bbox_detector": ("BBOX_DETECTOR",),
                "sam_model": ("SAM_MODEL",),
                "segm_detector": ("SEGM_DETECTOR",),
                "detailer_hook": ("DETAILER_HOOK",),
            },
        }

    RETURN_TYPES = ("DETAILER_PIPE",)
    RETURN_NAMES = ("detailer_pipe",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, *args, **kwargs):
        detailer_pipe = kwargs['detailer_pipe']
        wildcard = kwargs['wildcard']
        model = kwargs.get('model', None)
        clip = kwargs.get('clip', None)
        vae = kwargs.get('vae', None)
        positive = kwargs.get('positive', None)
        negative = kwargs.get('negative', None)
        bbox_detector = kwargs.get('bbox_detector', None)
        sam_model = kwargs.get('sam_model', None)
        segm_detector = kwargs.get('segm_detector', None)
        detailer_hook = kwargs.get('detailer_hook', None)
        refiner_model = kwargs.get('refiner_model', None)
        refiner_clip = kwargs.get('refiner_clip', None)
        refiner_positive = kwargs.get('refiner_positive', None)
        refiner_negative = kwargs.get('refiner_negative', None)

        res_model, res_clip, res_vae, res_positive, res_negative, res_wildcard, res_bbox_detector, res_segm_detector, res_sam_model, res_detailer_hook, res_refiner_model, res_refiner_clip, res_refiner_positive, res_refiner_negative = detailer_pipe

        if model is not None:
            res_model = model

        if clip is not None:
            res_clip = clip

        if vae is not None:
            res_vae = vae

        if positive is not None:
            res_positive = positive

        if negative is not None:
            res_negative = negative

        if bbox_detector is not None:
            res_bbox_detector = bbox_detector

        if segm_detector is not None:
            res_segm_detector = segm_detector

        if wildcard != "":
            res_wildcard = wildcard

        if sam_model is not None:
            res_sam_model = sam_model

        if detailer_hook is not None:
            res_detailer_hook = detailer_hook

        if refiner_model is not None:
            res_refiner_model = refiner_model

        if refiner_clip is not None:
            res_refiner_clip = refiner_clip

        if refiner_positive is not None:
            res_refiner_positive = refiner_positive

        if refiner_negative is not None:
            res_refiner_negative = refiner_negative

        pipe = (res_model, res_clip, res_vae, res_positive, res_negative, res_wildcard,
                res_bbox_detector, res_segm_detector, res_sam_model, res_detailer_hook,
                res_refiner_model, res_refiner_clip, res_refiner_positive, res_refiner_negative)

        return (pipe, )

```
