# ToDetailerPipe
## Documentation
- Class name: `ToDetailerPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `ToDetailerPipe` class is designed to encapsulate the functionality for transforming input components into a detailer pipe format. This process involves organizing and structuring various components such as models, clips, VAEs, and conditioning elements into a coherent pipeline that can be utilized for detailed image or text generation tasks.
## Input types
### Required
- **`model`**
    - The 'model' parameter represents the core generative model used in the pipeline. Its inclusion is crucial as it defines the backbone of the generation process, influencing the output's quality and characteristics.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The 'clip' parameter is used for semantic understanding and alignment between text and images, enhancing the relevance and accuracy of the generated output.
    - Python dtype: `str`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The 'vae' parameter stands for Variational Autoencoder. It's used for learning efficient data codings in an unsupervised manner, which is essential for generating or reconstructing images.
    - Python dtype: `str`
    - Comfy dtype: `VAE`
- **`positive`**
    - This parameter is used for specifying positive conditioning signals that guide the generation process towards desired attributes or features.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Similar to the 'positive' parameter, this one is used for specifying negative conditioning signals to steer away the generation from certain attributes or features.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - The 'bbox_detector' parameter is used for detecting bounding boxes in images. It's crucial for tasks that require understanding and manipulating specific regions within images.
    - Python dtype: `str`
    - Comfy dtype: `BBOX_DETECTOR`
- **`wildcard`**
    - A flexible parameter that allows for the inclusion of additional, unspecified data or instructions that can influence the generation process in various ways.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - This parameter allows for the selection of a LoRA (Locally Reweighted Affine transformation) to be added to the text, enabling fine-grained control over the text generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
- **`Select to add Wildcard`**
    - Allows for the selection of a wildcard to be added to the text, providing additional flexibility and control over the text generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['STRING']`
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
    - The output is a detailer pipe, which is a structured collection of components including models, clips, VAEs, and conditioning elements, organized for detailed generation tasks.
    - Python dtype: `Tuple`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FaceDetailerPipe,FromDetailerPipe`


## Source code
```python
class ToDetailerPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "model": ("MODEL",),
                     "clip": ("CLIP",),
                     "vae": ("VAE",),
                     "positive": ("CONDITIONING",),
                     "negative": ("CONDITIONING",),
                     "bbox_detector": ("BBOX_DETECTOR", ),
                     "wildcard": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                     "Select to add LoRA": (["Select the LoRA to add to the text"] + folder_paths.get_filename_list("loras"),),
                     "Select to add Wildcard": (["Select the Wildcard to add to the text"], ),
                     },
                "optional": {
                    "sam_model_opt": ("SAM_MODEL",),
                    "segm_detector_opt": ("SEGM_DETECTOR",),
                    "detailer_hook": ("DETAILER_HOOK",),
                }}

    RETURN_TYPES = ("DETAILER_PIPE", )
    RETURN_NAMES = ("detailer_pipe", )
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, *args, **kwargs):
        pipe = (kwargs['model'], kwargs['clip'], kwargs['vae'], kwargs['positive'], kwargs['negative'], kwargs['wildcard'], kwargs['bbox_detector'],
                kwargs.get('segm_detector_opt', None), kwargs.get('sam_model_opt', None), kwargs.get('detailer_hook', None),
                kwargs.get('refiner_model', None), kwargs.get('refiner_clip', None),
                kwargs.get('refiner_positive', None), kwargs.get('refiner_negative', None))
        return (pipe, )

```
