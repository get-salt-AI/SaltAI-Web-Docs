---
tags:
- LLM
- LlamaIndex
---

# ∞ Image Vision LLM
## Documentation
- Class name: `LLMImageVisionLLMReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMImageVisionLLMReader node is designed to process images, extracting and interpreting their content to generate documents that encapsulate the visual information in a structured format. It leverages advanced vision and language models to understand and describe the contents of an image, making it accessible for further analysis or integration into text-based data systems.
## Input types
### Required
- **`path`**
    - The 'path' parameter specifies the file system location of the image to be processed. It is essential for locating the image file and loading it for analysis.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - The 'extra_info' parameter allows for the inclusion of additional, user-defined information that can be utilized during the image processing. This flexibility enables customization of the processing based on specific requirements or contexts.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output 'documents' encapsulate the processed image information in a structured document format, making the visual data interpretable and usable in text-based applications.
    - Python dtype: `Tuple[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMImageVisionLLMReader(ImageVisionLLMReader):
    """
    @NOTE: Not sure what this does yet
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/image_vision_llm/base.py
    @Documentation:https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.ImageVisionLLMReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
                #"warning": ("STRING", {"default":"WARNING: This downloads a 15GB file in two parts."}),
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, warning:str, extra_info:str):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
