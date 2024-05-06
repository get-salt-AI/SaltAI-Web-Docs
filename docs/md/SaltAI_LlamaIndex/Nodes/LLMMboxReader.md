---
tags:
- LLM
- LlamaIndex
---

# ∞ Mbox
## Documentation
- Class name: `LLMMboxReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMMboxReader node specializes in reading email files from mbox format and converting them into a llama_index Document format, facilitating the processing and analysis of email data within the Llama Index ecosystem.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the mbox file to be read. This path is essential for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in JSON string format that can be used to customize the reading process or provide context-specific data.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Outputs the processed email data as a llama_index Document, ready for further analysis or integration within the Llama Index ecosystem.
    - Python dtype: `tuple[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMMboxReader(MboxReader):
    """
    @NOTE: Reads Email files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/mbox/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.MboxReader
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
