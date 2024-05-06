---
tags:
- LLM
- LlamaIndex
---

# ∞ IPYNB
## Documentation
- Class name: `LLMIPYNBReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMIPYNBReader node specializes in reading IPYNB (Jupyter Notebook) files and converting them into a format suitable for integration with the llama_index Document structure. This facilitates the extraction and indexing of information contained within IPYNB files for further processing or analysis.
## Input types
### Required
- **`path`**
    - Specifies the filesystem path to the IPYNB file to be read. This path is essential for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in a string format that can be used to influence the reading process or handle specific requirements for processing the IPYNB file.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed data from the IPYNB file, structured as a llama_index Document, ready for further indexing or analysis.
    - Python dtype: `Document`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMIPYNBReader(IPYNBReader):
    """
    @NOTE: Reads IPYNB documentation files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/ipynb/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.IPYNBReader
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
