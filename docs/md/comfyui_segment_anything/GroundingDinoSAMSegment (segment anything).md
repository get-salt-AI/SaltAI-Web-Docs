# GroundingDinoSAMSegment (segment anything)
## Documentation
- Class name: `GroundingDinoSAMSegment`
- Category: `segment_anything`
- Output node: `False`

The GroundingDinoSAMSegment node is designed for segmenting various objects within an image using the GroundingDINO model. It leverages advanced deep learning techniques, specifically a transformer-based architecture, to accurately identify and segment objects by understanding the context and details within the image. This node is part of a larger system that integrates with the GroundingDINO framework, which is known for its effectiveness in conditional object detection and segmentation tasks.
## Input types
### Required
- **`sam_model`**
    - Specifies the SAM model used for the segmentation of detected objects. This model refines the segmentation results provided by the GroundingDINO model.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `SAM_MODEL`
- **`grounding_dino_model`**
    - Specifies the GroundingDINO model to be used for object detection. This model is essential for identifying objects within the image based on the given prompt.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `GROUNDING_DINO_MODEL`
- **`image`**
    - The input image to be processed. The GroundingDINO model uses this image to detect objects based on the provided prompt.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`prompt`**
    - A text prompt that guides the GroundingDINO model in detecting specific objects within the image.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`threshold`**
    - A threshold value that determines the sensitivity of object detection. Higher values result in fewer detections.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
## Output types
- **`image`**
    - The images resulting from the segmentation process, containing the detected and segmented objects.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `IMAGE`
- **`mask`**
    - The masks corresponding to each segmented object in the images. These masks outline the boundaries of detected objects.
    - Python dtype: `List[torch.Tensor]`
    - Comfy dtype: `MASK`
## Usage tips
- Infra type: `GPU`
- Common nodes: `PreviewImage,Reroute,VAEEncodeForInpaint,MaskToImage,Mask Gaussian Region,ArithmeticBlend,InvertMask (segment anything),InvertMask,GrowMask`

The GroundingDinoSAMSegment node is designed for advanced image segmentation, leveraging the GroundingDINO and SAM models to accurately identify and segment objects within an image based on textual prompts. It takes as input an image, a SAM model, a Grounding Dino model, and a textual prompt, and outputs segmented parts of the image along with corresponding masks, enabling precise editing and manipulation tasks in pipelines focused on image enhancement, editing, or conditional manipulation.
## Source code
```python
class GroundingDinoSAMSegment:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "sam_model": ('SAM_MODEL', {}),
                "grounding_dino_model": ('GROUNDING_DINO_MODEL', {}),
                "image": ('IMAGE', {}),
                "prompt": ("STRING", {}),
                "threshold": ("FLOAT", {
                    "default": 0.3,
                    "min": 0,
                    "max": 1.0,
                    "step": 0.01
                }),
            }
        }
    CATEGORY = "segment_anything"
    FUNCTION = "main"
    RETURN_TYPES = ("IMAGE", "MASK")

    def main(self, grounding_dino_model, sam_model, image, prompt, threshold):
        res_images = []
        res_masks = []
        for item in image:
            item = Image.fromarray(
                np.clip(255. * item.cpu().numpy(), 0, 255).astype(np.uint8)).convert('RGBA')
            boxes = groundingdino_predict(
                grounding_dino_model,
                item,
                prompt,
                threshold
            )
            if boxes.shape[0] == 0:
                break
            (images, masks) = sam_segment(
                sam_model,
                item,
                boxes
            )
            res_images.extend(images)
            res_masks.extend(masks)
        if len(res_images) == 0:
            _, height, width, _ = image.size()
            empty_mask = torch.zeros((1, height, width), dtype=torch.uint8, device="cpu")
            return (empty_mask, empty_mask)
        return (torch.cat(res_images, dim=0), torch.cat(res_masks, dim=0))

```
