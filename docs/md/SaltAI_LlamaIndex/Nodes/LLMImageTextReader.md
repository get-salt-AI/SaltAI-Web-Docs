---
tags:
- LLM
- LlamaIndex
---

# ∞ Image Text Parser
## Documentation
- Class name: `LLMImageTextReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMImageTextReader node is designed to read and process images for text extraction and additional information parsing. It leverages the capabilities of the Llama Index's ImageReader to interpret images, optionally parse text within them, and handle extra information specified by the user, facilitating a comprehensive analysis of image-based documents.
## Input types
### Required
- **`path`**
    - Specifies the file path of the image to be processed. It is crucial for locating and accessing the image file for subsequent operations.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`parse_text`**
    - Determines whether the text within the image should be parsed. This option allows for flexibility in processing, depending on whether textual information extraction is needed.
    - Comfy dtype: `COMBO[BOOLEAN]`
    - Python dtype: `bool`
- **`extra_info`**
    - Allows for the inclusion of additional, user-defined information in a JSON string format, which can be utilized during the image processing for enhanced customization and functionality.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns the processed data from the image, which may include extracted text and any specified extra information, encapsulated in a document format.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMImageTextReader(ImageReader):
    """
    @NOTE: Not sure what this does yet
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/image/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.ImageReader
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
                "parse_text": ([False, True], {"default": False}),
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, parse_text:bool, extra_info:str, keep_image:bool=False, fs = None):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
    #	self._keep_image = keep_image
        self._parse_text = parse_text
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
