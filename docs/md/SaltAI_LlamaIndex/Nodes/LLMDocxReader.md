---
tags:
- DocumentConversion
- LLM
- LlamaIndex
---

# ∞ Docx
## Documentation
- Class name: `LLMDocxReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMDocxReader node specializes in reading Microsoft Word (.docx) files and converting them into a format compatible with the llama_index Document structure, facilitating the integration of text data from Word documents into the llama_index ecosystem for further processing or analysis.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the .docx file to be read. This path is essential for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Allows for the inclusion of additional, user-defined metadata in JSON string format, which can be appended to the document's metadata, enhancing the document's contextual information.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns a llama_index Document object, encapsulating the content and metadata extracted from the .docx file.
    - Python dtype: `Document`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMDocxReader(DocxReader):
    """
    @NOTE: Reads MS Word files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/docs/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.DocxReader
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
