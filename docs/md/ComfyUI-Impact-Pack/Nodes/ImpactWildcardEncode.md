# ImpactWildcardEncode
## Documentation
- Class name: `ImpactWildcardEncode`
- Category: `ImpactPack/Prompt`
- Output node: `False`

The `ImpactWildcardEncode` node is designed to process text inputs by replacing specific wildcard patterns with dynamically generated or predefined text segments. This process involves searching for wildcard patterns within the input text, matching these patterns against a dictionary of possible replacements, and substituting the found patterns with a randomly selected replacement or processing them further based on additional rules (e.g., wildcard normalization, handling special wildcard characters like '*', '+', and handling fallbacks). The node supports complex wildcard functionalities, including recursive replacement and handling of special wildcard formats for segmentation and ordering.
## Input types
### Required
- **`model`**
    - The model used in the process, potentially influencing the outcome of the wildcard replacement.
    - Python dtype: `Any`
    - Comfy dtype: `MODEL`
- **`clip`**
    - The CLIP model used in the process, potentially influencing the outcome of the wildcard replacement.
    - Python dtype: `Any`
    - Comfy dtype: `CLIP`
- **`wildcard_text`**
    - The primary text input containing wildcard patterns that need to be processed and replaced. The presence and nature of these wildcards directly influence the transformation of the input text, enabling dynamic content generation based on the wildcard dictionary.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`populated_text`**
    - The text after wildcards have been processed and replaced, serving as the output for further operations or as the final result.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
- **`mode`**
    - A boolean flag indicating whether to populate the text with dynamic content ('Populate') or to keep the text fixed ('Fixed').
    - Python dtype: `bool`
    - Comfy dtype: `BOOLEAN`
- **`Select to add LoRA`**
    - Allows selection of a specific LoRA to add to the text, enhancing the customization of the content generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['Select the LoRA to add to the text']`
- **`Select to add Wildcard`**
    - Allows selection of a specific wildcard to add to the text, enhancing the customization of the content generation process.
    - Python dtype: `List[str]`
    - Comfy dtype: `['Select the Wildcard to add to the text']`
- **`seed`**
    - An integer seed for random number generation, ensuring reproducibility of the wildcard replacement process.
    - Python dtype: `int`
    - Comfy dtype: `INT`
## Output types
- **`model`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `MODEL`
- **`clip`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CLIP`
- **`conditioning`**
    - unknown
    - Python dtype: `unknown`
    - Comfy dtype: `CONDITIONING`
- **`string`**
    - The processed text with all applicable wildcard patterns replaced by their corresponding replacements. This output reflects the dynamic text generation capabilities of the node, showcasing the impact of wildcard processing on the original input.
    - Python dtype: `str`
    - Comfy dtype: `STRING`
## Usage tips
- Infra type: `CPU`
- Common nodes: `KSampler,Reroute,CLIPTextEncode,FaceDetailer,ControlNetApplyAdvanced`


## Source code
```python
class ImpactWildcardEncode:
    @classmethod
    def INPUT_TYPES(s):
        return {"required": {
                        "model": ("MODEL",),
                        "clip": ("CLIP",),
                        "wildcard_text": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                        "populated_text": ("STRING", {"multiline": True, "dynamicPrompts": False}),
                        "mode": ("BOOLEAN", {"default": True, "label_on": "Populate", "label_off": "Fixed"}),
                        "Select to add LoRA": (["Select the LoRA to add to the text"] + folder_paths.get_filename_list("loras"), ),
                        "Select to add Wildcard": (["Select the Wildcard to add to the text"], ),
                        "seed": ("INT", {"default": 0, "min": 0, "max": 0xffffffffffffffff}),
                    },
                }

    CATEGORY = "ImpactPack/Prompt"

    RETURN_TYPES = ("MODEL", "CLIP", "CONDITIONING", "STRING")
    RETURN_NAMES = ("model", "clip", "conditioning", "populated_text")
    FUNCTION = "doit"

    @staticmethod
    def process_with_loras(**kwargs):
        return impact.wildcards.process_with_loras(**kwargs)

    @staticmethod
    def get_wildcard_list():
        return impact.wildcards.get_wildcard_list()

    def doit(self, *args, **kwargs):
        populated = kwargs['populated_text']
        model, clip, conditioning = impact.wildcards.process_with_loras(populated, kwargs['model'], kwargs['clip'])
        return (model, clip, conditioning, populated)

```
