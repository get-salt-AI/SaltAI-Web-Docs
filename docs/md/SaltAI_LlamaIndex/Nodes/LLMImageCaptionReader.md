---
tags:
- LLM
- LlamaIndex
---

# ∞ Image BLIP Caption
## Documentation
- Class name: `LLMImageCaptionReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMImageCaptionReader node is designed to interpret and describe images by generating captions that encapsulate the content and context of the image, transforming visual data into descriptive text within a llama_index Document format.
## Input types
### Required
- **`path`**
    - Specifies the file path to the image to be captioned, serving as the primary input for generating the image description.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`prompt`**
    - An optional text prompt that can guide or influence the caption generation process, providing context or specific instructions for the caption.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`extra_info`**
    - Additional information in string format that can be used to further customize the caption generation process or provide extra context.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The generated captions for the input images, encapsulated within llama_index Document objects.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMImageCaptionReader(ImageCaptionReader):
    """
    @NOTE: Describes the image file as if it were captioning it into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/image_caption/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.ImageCaptionReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
            },
            "optional": {
    #			"keep_image": ([False, True], {"default": False}),
                "prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": ""}),
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, prompt:str, extra_info:str, keep_image:bool=False):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
    #	self._keep_image = keep_image
        self._prompt = prompt
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
