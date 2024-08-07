---
tags:
- Segmentation
---

# Human Segmentation
## Documentation
- Class name: `easy humanSegmentation`
- Category: `EasyUse/Segmentation`
- Output node: `False`

This node is designed for segmenting human figures from images, utilizing a choice of pre-defined segmentation methods. It abstracts the complexity of human segmentation processes, offering an easy-to-use interface for extracting human figures with various levels of detail and precision.
## Input types
### Required
- **`image`**
    - The input image to be segmented. This parameter is crucial as it provides the raw data for the segmentation process.
    - Comfy dtype: `IMAGE`
    - Python dtype: `Image.Image`
- **`method`**
    - Specifies the segmentation method to be used. This choice affects the segmentation's accuracy and detail, allowing for customization based on the user's needs.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`confidence`**
    - Determines the confidence threshold for the segmentation process, influencing the precision of the human figure extraction.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`crop_multi`**
    - Adjusts the cropping multiplier to control the extent of the area around the segmented human figure that is included in the output.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`image`**
    - Comfy dtype: `IMAGE`
    - The segmented image with the human figure.
    - Python dtype: `Image.Image`
- **`mask`**
    - Comfy dtype: `MASK`
    - A binary mask of the segmented human figure.
    - Python dtype: `Image.Image`
- **`bbox`**
    - Comfy dtype: `BBOX`
    - Bounding box coordinates of the segmented human figure.
    - Python dtype: `Tuple[int, int, int, int]`
- **`ui`**
    - Provides a user interface component displaying the segmentation results.
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class humanSegmentation:

    @classmethod
    def INPUT_TYPES(cls):

        return {
          "required":{
            "image": ("IMAGE",),
            "method": (["selfie_multiclass_256x256", "human_parsing_lip"],),
            "confidence": ("FLOAT", {"default": 0.4, "min": 0.05, "max": 0.95, "step": 0.01},),
            "crop_multi": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 10.0, "step": 0.001},),
          },
          "hidden": {
              "prompt": "PROMPT",
              "my_unique_id": "UNIQUE_ID",
          }
        }

    RETURN_TYPES = ("IMAGE", "MASK", "BBOX")
    RETURN_NAMES = ("image", "mask", "bbox")
    FUNCTION = "parsing"
    CATEGORY = "EasyUse/Segmentation"

    def get_mediapipe_image(self, image: Image):
      import mediapipe as mp
      # Convert image to NumPy array
      numpy_image = np.asarray(image)
      image_format = mp.ImageFormat.SRGB
      # Convert BGR to RGB (if necessary)
      if numpy_image.shape[-1] == 4:
        image_format = mp.ImageFormat.SRGBA
      elif numpy_image.shape[-1] == 3:
        image_format = mp.ImageFormat.SRGB
        numpy_image = cv2.cvtColor(numpy_image, cv2.COLOR_BGR2RGB)
      return mp.Image(image_format=image_format, data=numpy_image)

    def parsing(self, image, confidence, method, crop_multi, prompt=None, my_unique_id=None):
      mask_components = []
      if my_unique_id in prompt:
        if prompt[my_unique_id]["inputs"]['mask_components']:
          mask_components = prompt[my_unique_id]["inputs"]['mask_components'].split(',')
      mask_components = list(map(int, mask_components))
      if method == 'selfie_multiclass_256x256':
        try:
          import mediapipe as mp
        except:
          install_package("mediapipe")
          import mediapipe as mp

        from functools import reduce

        model_path = get_local_filepath(MEDIAPIPE_MODELS['selfie_multiclass_256x256']['model_url'], MEDIAPIPE_DIR)
        model_asset_buffer = None
        with open(model_path, "rb") as f:
            model_asset_buffer = f.read()
        image_segmenter_base_options = mp.tasks.BaseOptions(model_asset_buffer=model_asset_buffer)
        options = mp.tasks.vision.ImageSegmenterOptions(
          base_options=image_segmenter_base_options,
          running_mode=mp.tasks.vision.RunningMode.IMAGE,
          output_category_mask=True)
        # Create the image segmenter
        ret_images = []
        ret_masks = []

        with mp.tasks.vision.ImageSegmenter.create_from_options(options) as segmenter:
            for img in image:
                _image = torch.unsqueeze(img, 0)
                orig_image = tensor2pil(_image).convert('RGB')
                # Convert the Tensor to a PIL image
                i = 255. * img.cpu().numpy()
                image_pil = Image.fromarray(np.clip(i, 0, 255).astype(np.uint8))
                # create our foreground and background arrays for storing the mask results
                mask_background_array = np.zeros((image_pil.size[0], image_pil.size[1], 4), dtype=np.uint8)
                mask_background_array[:] = (0, 0, 0, 255)
                mask_foreground_array = np.zeros((image_pil.size[0], image_pil.size[1], 4), dtype=np.uint8)
                mask_foreground_array[:] = (255, 255, 255, 255)
                # Retrieve the masks for the segmented image
                media_pipe_image = self.get_mediapipe_image(image=image_pil)
                segmented_masks = segmenter.segment(media_pipe_image)
                masks = []
                for i, com in enumerate(mask_components):
                    masks.append(segmented_masks.confidence_masks[com])

                image_data = media_pipe_image.numpy_view()
                image_shape = image_data.shape
                # convert the image shape from "rgb" to "rgba" aka add the alpha channel
                if image_shape[-1] == 3:
                    image_shape = (image_shape[0], image_shape[1], 4)
                mask_background_array = np.zeros(image_shape, dtype=np.uint8)
                mask_background_array[:] = (0, 0, 0, 255)
                mask_foreground_array = np.zeros(image_shape, dtype=np.uint8)
                mask_foreground_array[:] = (255, 255, 255, 255)
                mask_arrays = []
                if len(masks) == 0:
                    mask_arrays.append(mask_background_array)
                else:
                    for i, mask in enumerate(masks):
                        condition = np.stack((mask.numpy_view(),) * image_shape[-1], axis=-1) > confidence
                        mask_array = np.where(condition, mask_foreground_array, mask_background_array)
                        mask_arrays.append(mask_array)
                # Merge our masks taking the maximum from each
                merged_mask_arrays = reduce(np.maximum, mask_arrays)
                # Create the image
                mask_image = Image.fromarray(merged_mask_arrays)
                # convert PIL image to tensor image
                tensor_mask = mask_image.convert("RGB")
                tensor_mask = np.array(tensor_mask).astype(np.float32) / 255.0
                tensor_mask = torch.from_numpy(tensor_mask)[None,]
                _mask = tensor_mask.squeeze(3)[..., 0]

                _mask = tensor2pil(tensor_mask).convert('L')

                ret_image = RGB2RGBA(orig_image, _mask)
                ret_images.append(pil2tensor(ret_image))
                ret_masks.append(image2mask(_mask))

            output_image = torch.cat(ret_images, dim=0)
            mask = torch.cat(ret_masks, dim=0)

      elif method == "human_parsing_lip":
        from .human_parsing.run_parsing import HumanParsing
        onnx_path = os.path.join(folder_paths.models_dir, 'onnx')
        model_path = get_local_filepath(HUMANPARSING_MODELS['parsing_lip']['model_url'], onnx_path)
        parsing = HumanParsing(model_path=model_path)
        model_image = image.squeeze(0)
        model_image = model_image.permute((2, 0, 1))
        model_image = to_pil_image(model_image)

        map_image, mask = parsing(model_image, mask_components)

        mask = mask[:, :, :, 0]

        alpha = 1.0 - mask

        output_image, = JoinImageWithAlpha().join_image_with_alpha(image, alpha)

      # use crop
      bbox = [[0, 0, 0, 0]]
      if crop_multi > 0.0:
        output_image, mask, bbox = imageCropFromMask().crop(output_image, mask, crop_multi, crop_multi, 1.0)

      return (output_image, mask, bbox)

```
