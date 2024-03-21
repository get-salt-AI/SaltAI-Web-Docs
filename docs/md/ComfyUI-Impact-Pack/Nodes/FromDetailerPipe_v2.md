# FromDetailerPipe_v2
## Documentation
- Class name: `FromDetailerPipe_v2`
- Category: `ImpactPack/Pipe`
- Output node: `False`

The `FromDetailerPipe_v2` node processes a detailer pipe, extracting and returning various components such as models, clips, VAEs, conditioning elements, bounding box detectors, SAM models, segmentation detectors, and detailer hooks. It essentially unpacks the detailer pipe into its constituent parts for further use.
## Input types
### Required
- **`detailer_pipe`**
    - The detailer pipe is a complex structure containing various components like models, clips, VAEs, and more. It serves as the input from which the node extracts and returns the individual components.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any]`
    - Comfy dtype: `DETAILER_PIPE`
## Output types
- **`detailer_pipe`**
    - Returns the original detailer pipe as received in the input.
    - Python dtype: `Tuple[torch.nn.Module, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any, Any]`
    - Comfy dtype: `DETAILER_PIPE`
- **`model`**
    - Returns the model extracted from the detailer pipe.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`clip`**
    - Returns the CLIP model extracted from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CLIP`
- **`vae`**
    - Returns the VAE model extracted from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `VAE`
- **`conditioning`**
    - Returns the negative conditioning element extracted from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `CONDITIONING`
- **`bbox_detector`**
    - Returns the bounding box detector extracted from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `BBOX_DETECTOR`
- **`sam_model`**
    - Returns the SAM model extracted from the detailer pipe, if available.
    - Python dtype: `Any`
    - Comfy dtype: `SAM_MODEL`
- **`segm_detector`**
    - Returns the segmentation detector extracted from the detailer pipe, if available.
    - Python dtype: `Any`
    - Comfy dtype: `SEGM_DETECTOR`
- **`detailer_hook`**
    - Returns the detailer hook extracted from the detailer pipe.
    - Python dtype: `Any`
    - Comfy dtype: `DETAILER_HOOK`
## Usage tips
- Infra type: `CPU`
- Common nodes: `FaceDetailerPipe`


## Source code
```python
class FromDetailerPipe_v2:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {"detailer_pipe": ("DETAILER_PIPE",), }, }

    RETURN_TYPES = ("DETAILER_PIPE", "MODEL", "CLIP", "VAE", "CONDITIONING", "CONDITIONING", "BBOX_DETECTOR", "SAM_MODEL", "SEGM_DETECTOR", "DETAILER_HOOK")
    RETURN_NAMES = ("detailer_pipe", "model", "clip", "vae", "positive", "negative", "bbox_detector", "sam_model_opt", "segm_detector_opt", "detailer_hook")
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Pipe"

    def doit(self, detailer_pipe):
        model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, _, _, _, _ = detailer_pipe
        return detailer_pipe, model, clip, vae, positive, negative, bbox_detector, sam_model_opt, segm_detector_opt, detailer_hook

```
