---
tags:
- LLM
- LlamaIndex
---

# ∞ Flat
## Documentation
- Class name: `LLMFlatReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMFlatReader node is designed to read and process 'flat' files, converting them into a format suitable for use with the llama_index Document structure. It leverages the capabilities of the base FlatReader class to facilitate the ingestion of simple text files into a more structured and analyzable form.
## Input types
### Required
- **`path`**
    - Specifies the file path to the 'flat' file that needs to be read. This path is essential for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in a string format that can be used to augment the reading process or the resulting document structure.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed data from the 'flat' file, structured as a llama_index Document, ready for further analysis or integration.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMFlatReader(FlatReader):
    """
    @NOTE: Reads 'flat' files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/flat/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.FlatReader
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
