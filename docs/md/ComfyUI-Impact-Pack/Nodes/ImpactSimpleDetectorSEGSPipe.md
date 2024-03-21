# Simple Detector (SEGS/pipe)
## Documentation
- Class name: `ImpactSimpleDetectorSEGSPipe`
- Category: `ImpactPack/Detector`
- Output node: `False`

The ImpactSimpleDetectorSEGSPipe node is designed for processing images through a simple detection pipeline, specifically tailored for SEGS (segmentation) data. It applies a straightforward detection algorithm to each image in a pipeline, facilitating the segmentation of images into distinct segments or objects. This node is particularly useful in workflows where rapid, basic segmentation is required without the need for complex detection models.
## Input types
### Required
- **`detailer_pipe`**
    - The detailer_pipe is a comprehensive set of models and configurations used throughout the detection process. It includes models for bounding box detection, segmentation, SAM model options, and more, enabling a versatile and detailed approach to image segmentation.
    - Python dtype: `Tuple[torch.nn.Module, ...]`
    - Comfy dtype: `DETAILER_PIPE`
- **`image`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `IMAGE`
- **`bbox_threshold`**
    - Determines the sensitivity of the bounding box detection. Lower values may detect more boxes, potentially including noise, while higher values result in fewer, more confident detections.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_dilation`**
    - Expands the detected bounding boxes, potentially capturing more of the object or merging close objects for further processing.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`crop_factor`**
    - Adjusts the size of the crop around detected objects, influencing the area of the image that is considered for segmentation.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Specifies the minimum size for detected objects, filtering out smaller detections to focus on more significant segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_threshold`**
    - Applies a threshold for sub-segmentation processes, refining the segmentation within the initially detected areas.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`sub_dilation`**
    - Used in sub-segmentation to expand the detected segments, similar to the main dilation parameter but applied at a different stage.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sub_bbox_expansion`**
    - Expands the bounding boxes in the sub-segmentation phase, potentially capturing more details or merging nearby segments.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`sam_mask_hint_threshold`**
    - Threshold for the SAM model to generate hints for segmentation, influencing how the model interprets the image for segmentation cues.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
### Optional
- **`post_dilation`**
    - Applies additional dilation after all segmentation processes are complete, potentially merging segments or making them more pronounced for the final output.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`segs`**
    - This output represents the segmented parts of the input image, identified by the detection model. It is essential for further processing or analysis of the image's distinct segments.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class SimpleDetectorForEachPipe:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "detailer_pipe": ("DETAILER_PIPE", ),
                        "image": ("IMAGE", ),

                        "bbox_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "bbox_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),

                        "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                        "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 10}),

                        "sub_threshold": ("FLOAT", {"default": 0.5, "min": 0.0, "max": 1.0, "step": 0.01}),
                        "sub_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                        "sub_bbox_expansion": ("INT", {"default": 0, "min": 0, "max": 1000, "step": 1}),

                        "sam_mask_hint_threshold": ("FLOAT", {"default": 0.7, "min": 0.0, "max": 1.0, "step": 0.01}),
                      },
                "optional": {
                        "post_dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                      }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Detector"

    def doit(self, detailer_pipe, image, bbox_threshold, bbox_dilation, crop_factor, drop_size,
             sub_threshold, sub_dilation, sub_bbox_expansion, sam_mask_hint_threshold, post_dilation=0):

        if len(image) > 1:
            raise Exception('[Impact Pack] ERROR: SimpleDetectorForEach does not allow image batches.\nPlease refer to https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/batching-detailer.md for more information.')

        model, clip, vae, positive, negative, wildcard, bbox_detector, segm_detector_opt, sam_model_opt, detailer_hook, refiner_model, refiner_clip, refiner_positive, refiner_negative = detailer_pipe

        return SimpleDetectorForEach.detect(bbox_detector, image, bbox_threshold, bbox_dilation, crop_factor, drop_size,
                                            sub_threshold, sub_dilation, sub_bbox_expansion,
                                            sam_mask_hint_threshold, post_dilation=post_dilation, sam_model_opt=sam_model_opt, segm_detector_opt=segm_detector_opt,
                                            detailer_hook=detailer_hook)

```
