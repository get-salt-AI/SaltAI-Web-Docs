# MediaPipe FaceMesh to SEGS
## Documentation
- Class name: `MediaPipeFaceMeshToSEGS`
- Category: `ImpactPack/Operation`
- Output node: `False`

This node transforms facial landmarks detected by MediaPipe FaceMesh into a format compatible with the SEGS (Segmentation) system. It involves processing the facial landmarks to generate segmentation masks for various facial features, adjusting them based on provided parameters such as crop factor, bounding box fill, and dilation.
## Input types
### Required
- **`image`**
    - The input image on which facial landmark detection and segmentation will be performed. It's crucial for detecting facial features accurately.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`crop_factor`**
    - Determines how much the detected facial feature areas are cropped. Affects the size of the segmentation masks generated for each facial feature.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`bbox_fill`**
    - Specifies how the bounding boxes around detected facial features are filled. Influences the segmentation mask's coverage area.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`crop_min_size`**
    - Sets the minimum size for cropping around detected facial features, ensuring that segmentation masks are not too small.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`drop_size`**
    - Defines the threshold for dropping small detected features, helping to filter out noise in the segmentation masks.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`dilation`**
    - Controls the dilation of the segmentation masks, allowing for adjustments in the mask boundaries for better coverage of facial features.
    - Python dtype: `int`
    - Comfy dtype: `INT`
- **`face`**
    - Indicates whether to include the face in the segmentation process, enabling or disabling the generation of a face mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`mouth`**
    - Determines whether the mouth area is included in the segmentation, affecting the generation of a mouth mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`left_eyebrow`**
    - Specifies whether to include the left eyebrow in the segmentation, influencing the generation of a left eyebrow mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`left_eye`**
    - Indicates whether to include the left eye in the segmentation, affecting the generation of a left eye mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`left_pupil`**
    - Determines whether to include the left pupil in the segmentation, influencing the generation of a left pupil mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`right_eyebrow`**
    - Specifies whether to include the right eyebrow in the segmentation, influencing the generation of a right eyebrow mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`right_eye`**
    - Indicates whether to include the right eye in the segmentation, affecting the generation of a right eye mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`right_pupil`**
    - Determines whether to include the right pupil in the segmentation, influencing the generation of a right pupil mask.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
## Output types
- **`segs`**
    - The output is a set of segmentation masks corresponding to the specified facial features, formatted to be compatible with the SEGS system.
    - Python dtype: `Tuple[torch.Tensor, List[torch.Tensor]]`
    - Comfy dtype: `SEGS`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class MediaPipeFaceMeshToSEGS:
    @classmethod
    def INPUT_TYPES(s):
        bool_true_widget = ("BOOLEAN", {"default": True, "label_on": "Enabled", "label_off": "Disabled"})
        bool_false_widget = ("BOOLEAN", {"default": False, "label_on": "Enabled", "label_off": "Disabled"})
        return {"required": {
                                "image": ("IMAGE",),
                                "crop_factor": ("FLOAT", {"default": 3.0, "min": 1.0, "max": 100, "step": 0.1}),
                                "bbox_fill": ("BOOLEAN", {"default": False, "label_on": "enabled", "label_off": "disabled"}),
                                "crop_min_size": ("INT", {"min": 10, "max": MAX_RESOLUTION, "step": 1, "default": 50}),
                                "drop_size": ("INT", {"min": 1, "max": MAX_RESOLUTION, "step": 1, "default": 1}),
                                "dilation": ("INT", {"default": 0, "min": -512, "max": 512, "step": 1}),
                                "face": bool_true_widget,
                                "mouth": bool_false_widget,
                                "left_eyebrow": bool_false_widget,
                                "left_eye": bool_false_widget,
                                "left_pupil": bool_false_widget,
                                "right_eyebrow": bool_false_widget,
                                "right_eye": bool_false_widget,
                                "right_pupil": bool_false_widget,
                             },
                # "optional": {"reference_image_opt": ("IMAGE", ), }
                }

    RETURN_TYPES = ("SEGS",)
    FUNCTION = "doit"

    CATEGORY = "ImpactPack/Operation"

    def doit(self, image, crop_factor, bbox_fill, crop_min_size, drop_size, dilation, face, mouth, left_eyebrow, left_eye, left_pupil, right_eyebrow, right_eye, right_pupil):
        # padding is obsolete now
        # https://github.com/Fannovel16/comfyui_controlnet_aux/blob/1ec41fceff1ee99596445a0c73392fd91df407dc/utils.py#L33
        # def calc_pad(h_raw, w_raw):
        #     resolution = normalize_size_base_64(h_raw, w_raw)
        #
        #     def pad64(x):
        #         return int(np.ceil(float(x) / 64.0) * 64 - x)
        #
        #     k = float(resolution) / float(min(h_raw, w_raw))
        #     h_target = int(np.round(float(h_raw) * k))
        #     w_target = int(np.round(float(w_raw) * k))
        #
        #     return pad64(h_target), pad64(w_target)

        # if reference_image_opt is not None:
        #     if image.shape[1:] != reference_image_opt.shape[1:]:
        #         scale_by1 = reference_image_opt.shape[1] / image.shape[1]
        #         scale_by2 = reference_image_opt.shape[2] / image.shape[2]
        #         scale_by = min(scale_by1, scale_by2)
        #
        #         # padding is obsolete now
        #         # h_pad, w_pad = calc_pad(reference_image_opt.shape[1], reference_image_opt.shape[2])
        #         # if h_pad != 0:
        #         #     # height padded
        #         #     image = image[:, :-h_pad, :, :]
        #         # elif w_pad != 0:
        #         #     # width padded
        #         #     image = image[:, :, :-w_pad, :]
        #
        #         image = nodes.ImageScaleBy().upscale(image, "bilinear", scale_by)[0]

        result = core.mediapipe_facemesh_to_segs(image, crop_factor, bbox_fill, crop_min_size, drop_size, dilation, face, mouth, left_eyebrow, left_eye, left_pupil, right_eyebrow, right_eye, right_pupil)
        return (result, )

```
