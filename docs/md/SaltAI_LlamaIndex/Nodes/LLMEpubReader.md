---
tags:
- DocumentConversion
- LLM
- LlamaIndex
---

# ∞ Epub
## Documentation
- Class name: `LLMEpubReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMEpubReader node specializes in reading Epub book files and converting them into a format compatible with the llama_index Document structure, facilitating the integration of Epub content into the llama_index ecosystem.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the Epub file to be read. It is essential for locating the file to be processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in a string format that can be used to influence the reading process or handle specific requirements.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns the content of the Epub file as a llama_index Document, making it accessible for further processing or analysis within the llama_index ecosystem.
    - Python dtype: `tuple[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMEpubReader(EpubReader):
    """
    @NOTE: Reads Epub book files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/epub/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.EpubReader
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

    def execute(self, path:str, extra_info:str, fs = None):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
