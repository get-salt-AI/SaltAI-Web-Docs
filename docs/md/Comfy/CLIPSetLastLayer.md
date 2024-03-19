# CLIP Set Last Layer
## Documentation
- Class name: `CLIPSetLastLayer`
- Category: `conditioning`
- Output node: `False`

The `CLIPSetLastLayer` node is designed to modify a CLIP model by setting its last layer to stop at a specified layer index. This operation is useful for controlling the depth of the model's processing, potentially affecting the model's performance and the characteristics of the generated embeddings.
## Input types
### Required
- **`clip`**
    - The `clip` parameter represents the CLIP model to be modified. It is crucial for defining the model on which the layer adjustment will be performed.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
- **`stop_at_clip_layer`**
    - The `stop_at_clip_layer` parameter specifies the index of the last layer to be used in the CLIP model. This allows for fine-tuning the depth of the model's processing, which can influence the model's output and performance.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`clip`**
    - Returns the modified CLIP model with the last layer set to the specified index. This adjusted model can then be used for further processing or embedding generation.
    - Python dtype: `torch.nn.Module`
    - Comfy dtype: `CLIP`
## Usage tips
- Infra type: `GPU`
- Common nodes: `CLIPTextEncode,LoraLoader,CR Apply LoRA Stack,Text to Conditioning,Reroute,PromptControlSimple,BatchPromptSchedule,CLIPTextEncodeA1111,FaceDetailer,BNK_CutoffBasePrompt`


## Source code
```python
class CLIPSetLastLayer:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": { "clip": ("CLIP", ),
                              "stop_at_clip_layer": ("INT", {"default": -1, "min": -24, "max": -1, "step": 1}),
                              }}
    RETURN_TYPES = ("CLIP",)
    FUNCTION = "set_last_layer"

    CATEGORY = "conditioning"

    def set_last_layer(self, clip, stop_at_clip_layer):
        clip = clip.clone()
        clip.clip_layer(stop_at_clip_layer)
        return (clip,)

```
