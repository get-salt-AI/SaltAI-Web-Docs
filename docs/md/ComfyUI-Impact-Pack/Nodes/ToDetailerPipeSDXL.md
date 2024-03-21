# ToDetailerPipeSDXL
## Documentation
- Class name: `ToDetailerPipeSDXL`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `ToDetailerPipeSDXL` class is designed to configure and prepare the detailer pipeline for execution, specifically tailored for the SDXL context. It involves setting up various models and parameters required for the detailer process, including models for image refinement, conditioning parameters for positive and negative influences, and optional components like segmentation detectors and hooks for further customization.
## Input types
### Required
- **`model`**
    - Specifies the primary model used in the detailer pipeline. It is crucial for the initial stages of image processing and refinement.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Defines the CLIP model used for semantic image analysis and guidance within the pipeline.
    - Python dtype: `str`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Indicates the VAE (Variational Autoencoder) model employed for encoding and decoding images in the pipeline.
    - Python dtype: `str`
    - Comfy dtype: `VAE`
- **`positive`**
    - Sets the positive conditioning parameters to guide the image generation towards desired attributes.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`negative`**
    - Sets the negative conditioning parameters to steer the image generation away from undesired attributes.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`refiner_model`**
    - Specifies an additional model for refining the details of the generated images, enhancing their quality.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`refiner_clip`**
    - Defines an additional CLIP model for refined semantic analysis in the later stages of the pipeline.
    - Python dtype: `str`
    - Comfy dtype: `CLIP`
- **`refiner_positive`**
    - Sets additional positive conditioning parameters for further refinement of the image generation process.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`refiner_negative`**
    - Sets additional negative conditioning parameters for further refinement, ensuring avoidance of undesired attributes in the final output.
    - Python dtype: `str`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - Incorporates a bounding box detector for identifying specific regions of interest within images, aiding in focused refinement.
    - Python dtype: `str`
    - Comfy dtype: `BBOX_DETECTOR`
- **`wildcard`**
    - Allows for the inclusion of dynamic, user-defined text inputs that can influence the generation process in versatile ways.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`Select to add LoRA`**
    - Enables the selection of LoRA (Low-Rank Adaptation) techniques to be applied, enhancing the model's adaptability to specific text inputs.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
- **`Select to add Wildcard`**
    - Facilitates the selection of wildcard options to introduce additional, user-defined influences on the generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `STRING`
### Optional
- **`sam_model_opt`**
    - Optionally specifies a SAM model for advanced image analysis and processing within the pipeline.
    - Python dtype: `str`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector_opt`**
    - Optionally includes a segmentation detector for detailed analysis and segmentation of image regions.
    - Python dtype: `str`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - Optionally integrates a custom hook for further customization and control over the detailer pipeline's execution.
    - Python dtype: `str`
    - Comfy dtype: `DETAILER_HOOK`
## Output types
- **`detailer_pipe`**
    - Outputs the configured detailer pipeline, ready for execution within the SDXL context.
    - Python dtype: `Tuple`
    - Comfy dtype: `DETAILER_PIPE`
## Usage tips
- Infra type: `GPU`
- Common nodes: `FromDetailerPipe_v2`


## Source code
```python
class ToDetailerPipeSDXL(ToDetailerPipe):
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                     "model": ("MODEL",),
                     "clip": ("CLIP",),
                     "vae": ("VAE",),
                     "positive": ("CONDITIONING",),
                     "negative": ("CONDITIONING",),
                     "refiner_model": ("MODEL",),
                     "refiner_clip": ("CLIP",),
                     "refiner_positive": ("CONDITIONING",),
                     "refiner_negative": ("CONDITIONING",),
                     "bbox_detector": ("BBOX_DETECTOR", ),
                     "wildcard": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                     "Select to add LoRA": (["Select the LoRA to add to the text"] + folder_paths.get_filename_list("loras"),),
                     "Select to add Wildcard": (["Select the Wildcard to add to the text"],),
                     },
                "optional": {
                    "sam_model_opt": ("SAM_MODEL",),
                    "segm_detector_opt": ("SEGM_DETECTOR",),
                    "detailer_hook": ("DETAILER_HOOK",),
                }}

```
