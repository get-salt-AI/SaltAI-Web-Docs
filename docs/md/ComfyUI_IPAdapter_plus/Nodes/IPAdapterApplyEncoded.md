# Apply IPAdapter from Encoded
## Documentation
- Class name: `IPAdapterApplyEncoded`
- Category: `ipadapter`
- Output node: `False`

This node applies an IPAdapter from encoded embeddings to a model, adjusting the model's behavior based on the embeddings and various parameters. It allows for the dynamic modification of a model's internal representations using external data, with options to control the intensity and manner of this modification.
## Input types
### Required
- **`ipadapter`**
    - The IPAdapter to be applied. It serves as a bridge between the external embeddings and the model, enabling the modification of the model's internal representations.
    - Python dtype: `IPAdapter`
    - Comfy dtype: `IPADAPTER`
- **`embeds`**
    - The embeddings that contain the encoded information to be applied to the model. These embeddings are crucial for the adaptation process, providing the specific adjustments to be made.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `EMBEDS`
- **`model`**
    - The model to which the IPAdapter and embeddings are applied. This parameter specifies the target of the adaptation, allowing for its behavior to be modified.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
- **`weight`**
    - A scalar that adjusts the intensity of the adaptation applied to the model. It allows for fine-tuning the impact of the embeddings on the model's behavior.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`weight_type`**
    - Specifies the method of applying the weight to the adaptation process. It influences how the embeddings modify the model, offering different strategies for integration.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`start_at`**
    - Defines the starting point of the adaptation in the model's processing. It allows for control over when the modifications begin to take effect.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`end_at`**
    - Determines the endpoint of the adaptation within the model's processing. This parameter controls the duration and extent of the modifications.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`unfold_batch`**
    - A boolean flag that indicates whether to process each item in a batch separately. It affects the adaptation's application across multiple inputs.
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
### Optional
- **`attn_mask`**
    - An optional mask that can be applied to the adaptation process. It allows for selective modification of the model's behavior, focusing the adjustments on specific areas.
    - Python dtype: `torch.Tensor`
    - Comfy dtype: `MASK`
## Output types
- **`model`**
    - The model after the application of the IPAdapter and embeddings. It represents the modified version of the original model, with altered behavior based on the provided parameters.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: `KSampler,CR Apply LoRA Stack,Reroute,ADE_AnimateDiffLoaderWithContext`

The IPAdapterApplyEncoded node dynamically adjusts a model's behavior by applying encoded embeddings, allowing for the customization of image features or styles during AI-driven image transformation processes. It takes an IPAdapter model, embeddings, and optionally an attention mask as input, producing a model with modified output, ideal for creating highly detailed and stylized images.
## Source code
```python
class IPAdapterApplyEncoded(IPAdapterApply):
    @classmethod
    def INPUT_TYPES(s):
        return {
            "required": {
                "ipadapter": ("IPADAPTER", ),
                "embeds": ("EMBEDS",),
                "model": ("MODEL", ),
                "weight": ("FLOAT", { "default": 1.0, "min": -1, "max": 3, "step": 0.05 }),
                "weight_type": (["original", "linear", "channel penalty"], ),
                "start_at": ("FLOAT", { "default": 0.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "end_at": ("FLOAT", { "default": 1.0, "min": 0.0, "max": 1.0, "step": 0.001 }),
                "unfold_batch": ("BOOLEAN", { "default": False }),
            },
            "optional": {
                "attn_mask": ("MASK",),
            }
        }

```
