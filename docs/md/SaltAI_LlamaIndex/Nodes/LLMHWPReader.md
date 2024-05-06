---
tags:
- LLM
- LlamaIndex
---

# ∞ HWP
## Documentation
- Class name: `LLMHWPReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMHWPReader node specializes in reading HWP (Korean) files and converting them into a format compatible with the llama_index Document structure. It is designed to facilitate the ingestion of HWP file content into the llama_index ecosystem for further processing or analysis.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the HWP file to be read. It is essential for locating the file that needs to be processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional information in a string format that can be used to influence the reading process. This could include metadata or configuration options.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output is a document structure compatible with the llama_index ecosystem, representing the content of the read HWP file.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMHWPReader(HWPReader):
    """
    @NOTE: Reads HWP (Korean) files into a llama_index Document
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.HWPReader
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

    def execute(self, path:str,  extra_info:str, fs = None):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
