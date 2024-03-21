# FromDetailer (SDXL/pipe)
## Documentation
- Class name: `FromDetailerPipeSDXL`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `FromDetailerPipeSDXL` node processes a detailer pipe, extracting and returning various components such as models, clips, VAEs, conditioning elements, bounding box detectors, SAM models, segmentation detectors, and detailer hooks. It essentially unpacks the detailer pipe into its constituent parts for further use or analysis.
## Input types
### Required
- **`detailer_pipe`**
    - The detailer pipe is a complex data structure that encapsulates various components necessary for detailer operations, including models, clips, VAEs, and more. It serves as the input from which the node extracts and returns individual components.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, str, str, str, Any, Optional[torch.nn.Module], Optional[Any], Optional[Any], Optional[torch.nn.Module], Optional[Any], Optional[str], Optional[str]]`
    - Comfy dtype: `DETAILER_PIPE`
## Output types
- **`detailer_pipe`**
    - Returns the original detailer pipe as received in the input.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, str, str, str, Any, Optional[torch.nn.Module], Optional[Any], Optional[Any], Optional[torch.nn.Module], Optional[Any], Optional[str], Optional[str]]`
    - Comfy dtype: `DETAILER_PIPE`
- **`model`**
    - An optional refiner model included in the detailer pipe.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `MODEL`
- **`clip`**
    - An optional refiner CLIP model included in the detailer pipe.
    - Python dtype: `Optional[Any]`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The VAE model used in the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
- **`conditioning`**
    - Optional negative conditioning elements for the refiner included in the detailer pipe.
    - Python dtype: `Optional[str]`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - The bounding box detector used in the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `BBOX_DETECTOR`
- **`sam_model`**
    - An optional SAM model included in the detailer pipe.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector`**
    - An optional segmentation detector included in the detailer pipe.
    - Python dtype: `Optional[Any]`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - An optional detailer hook included in the detailer pipe.
    - Python dtype: `Optional[Any]`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class FromDetailerPipe_SDXL:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"detailer_pipe": ("DETAILER_PIPE",), }, }

    RETURN_TYPES = ("DETAILER_PIPE", "MODEL", "CLIP", "VAE", "CONDITIONING", "CONDITIONING", "BBOX_DETECTOR", "SAM_MODEL", "SEGM_DETECTOR", "DETAILER_HOOK", "MODEL", "CLIP", "CONDITIONING", "CONDITIONING")
    RETURN_NAMES = ("detailer_pipe", "model", "clip", "vae", "positive", "negative", "bbox_detector", "sam_model_opt", "segm_detector_opt", "detailer_hook", "refiner_model", "refiner_clip", "refiner_positive", "refiner_negative")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, detailer_pipe):
        model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, refiner_model, refiner_clip, refiner_positive, refiner_negative = detailer_pipe
        return detailer_pipe, model, clip, vae, positive, negative, bbox_detector, sam_model_opt, segm_detector_opt, detailer_hook, refiner_model, refiner_clip, refiner_positive, refiner_negative

```
