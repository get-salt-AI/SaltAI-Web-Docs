# Apply IPAdapter FaceID
## Documentation
- Class name: `IPAdapterApplyFaceID`
- Category: `ipadapter`
- Output node: `False`

IPAdapterApplyFaceID extends the functionality of IPAdapterApply to incorporate face identification features into the image processing pipeline. It leverages InsightFace for face detection and embedding, adjusting the image based on detected faces to enhance or modify facial features according to specified parameters.
## Input types
### Required
- **`ipadapter`**
    - The IPAdapter model to be applied. It's crucial for adapting the input image in accordance with the model's capabilities and configurations.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `IPADAPTER`
- **`clip_vision`**
    - A CLIP vision model used for generating embeddings from images. This model plays a key role in aligning the image content with textual descriptions or other images.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP_VISION`
- **`insightface`**
    - InsightFace model for face detection and embedding. It's essential for identifying and processing faces within the image, especially for FaceID models.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `INSIGHTFACE`
- **`image`**
    - The input image to be processed. This is the primary data on which the IPAdapter and InsightFace models operate.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `IMAGE`
- **`model`**
    - The generative model to be used in conjunction with the IPAdapter. It defines the overall structure and capabilities of the image generation process.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`weight`**
    - A floating-point value that influences the blending of conditions in the generation process. It affects how strongly the specified conditions are applied to the generated image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`noise`**
    - A parameter to control the addition of noise to the image. This can be used to introduce variability or to simulate certain effects.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`weight_type`**
    - Specifies the method for applying weights to the generation process. Options include 'original', 'linear', and 'channel penalty', each affecting the blending and emphasis of conditions differently.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`start_at`**
    - Defines the starting point of the generation process in terms of model sampling. It can be used to control the level of abstraction or detail in the generated image.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_at`**
    - Defines the ending point of the generation process in terms of model sampling. It affects the final appearance of the generated image by controlling the sampling resolution.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`faceid_v2`**
    - A boolean flag indicating whether to use an updated version of the FaceID feature. This can affect the face detection and processing algorithms.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`weight_v2`**
    - A floating-point value for the updated version of the weight parameter. It allows for finer control over the blending and application of conditions in the generation process.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`unfold_batch`**
    - A boolean flag that controls the batching behavior during processing. Enabling this can improve performance by processing multiple items in parallel.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`attn_mask`**
    - An optional mask that can be applied to the attention mechanism of the model. This allows for selective focus or exclusion of certain parts of the image during processing.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`model`**
    - The modified generative model after applying the IPAdapter with FaceID features. It reflects the adjustments made to incorporate facial identification and processing.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler //Inspire,FreeU_V2,KSampler,Anything Everywhere,IPAdapterApply,ToIPAdapterPipe //Inspire,ToDetailerPipe,CR Apply LoRA Stack,IPAdapterApplyFaceID,FaceDetailer`

The IPAdapterApplyFaceID node is designed for enhancing or modifying facial features in images by leveraging InsightFace for face detection and embedding. It inputs an IPAdapter model, CLIP vision features, InsightFace features, an image, and optionally an attention mask, and outputs a model adapted for precise facial identification and processing, ideal for applications requiring detailed face adjustments.
## Source code
```python
class IPAdapterApplyFaceID(IPAdapterApply):
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "ipadapter": ("IPADAPTER", ),
                "clip_vision": ("CLIP_VISION",),
                "insightface": ("INSIGHTFACE",),
                "image": ("IMAGE",),
                "model": ("MODEL", ),
                "weight": ("FLOAT", { "default": 1.0, "min": -1, "max": 3, "step": 0.05 }),
                "noise": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.01 }),
                "weight_type": (["original", "linear", "channel penalty"], ),
                "start_at": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "end_at": ("FLOAT", { "default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "faceid_v2": ("BOOLEAN", { "default": False }),
                "weight_v2": ("FLOAT", { "default": 1.0, "min": -1, "max": 3, "step": 0.05 }),
                "unfold_batch": ("BOOLEAN", { "default": False }),
            },
            "optional": {
                "attn_mask": ("MASK",),
            }
        }

```
