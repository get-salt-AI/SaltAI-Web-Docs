---
tags:
- DocumentConversion
- LLM
- LlamaIndex
---

# ∞ RTF
## Documentation
- Class name: `LLMRTFReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMRTFReader node specializes in reading RTF (Rich Text Format) files and converting them into a format compatible with the llama_index Document structure, facilitating the integration of rich text content into the llama_index ecosystem.
## Input types
### Required
- **`path`**
    - Specifies the file path to the RTF document to be read. It is essential for locating the file to be processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional information as a string that can be used to augment the reading process, although its use is optional.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Outputs the processed document in a format that is compatible with the llama_index ecosystem.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMRTFReader(RTFReader):
    """
    @NOTE: Reads RTF rich text files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/rtf/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.RTFReader
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
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, extra_info:str):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
