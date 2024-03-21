# FromDetailerPipe
## Documentation
- Class name: `FromDetailerPipe`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `FromDetailerPipe` class processes a detailer pipe, extracting and returning various components such as models, clips, VAEs, conditioning elements, bounding box detectors, SAM models, segmentation detectors, and detailer hooks. This operation is essential for decomposing a complex pipeline into its constituent parts for further processing or analysis.
## Input types
### Required
- **`detailer_pipe`**
    - The `detailer_pipe` parameter is a complex data structure that encapsulates various components of a detailer pipeline. It is crucial for the operation as it contains all the necessary elements to be extracted and returned.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any, Any, torch.nn.Module, Optional[torch.nn.Module], Optional[torch.nn.Module], Any, Any, Any, Any, Any]`
    - Comfy dtype: `DETAILER_PIPE`
## Output types
- **`model`**
    - The extracted model component from the detailer pipe.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The extracted CLIP model component from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CLIP`
- **`vae`**
    - The extracted VAE model component from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
- **`conditioning`**
    - The extracted negative conditioning component from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - The extracted bounding box detector component from the detailer pipe.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `BBOX_DETECTOR`
- **`sam_model`**
    - The optionally extracted SAM model component from the detailer pipe.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector`**
    - The optionally extracted segmentation detector component from the detailer pipe.
    - Python dtype: `Optional[torch.nn.Module]`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - The extracted detailer hook component from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `DetailerForEachDebug,ToBasicPipe,CLIPTextEncode`


## Source code
```python
class FromDetailerPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"detailer_pipe": ("DETAILER_PIPE",), }, }

    RETURN_TYPES = ("MODEL", "CLIP", "VAE", "CONDITIONING", "CONDITIONING", "BBOX_DETECTOR", "SAM_MODEL", "SEGM_DETECTOR", "DETAILER_HOOK")
    RETURN_NAMES = ("model", "clip", "vae", "positive", "negative", "bbox_detector", "sam_model_opt", "segm_detector_opt", "detailer_hook")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, detailer_pipe):
        model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, _, _, _, _ = detailer_pipe
        return model, clip, vae, positive, negative, bbox_detector, sam_model_opt, segm_detector_opt, detailer_hook

```
