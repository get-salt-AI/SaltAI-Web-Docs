---
tags:
- DocumentConversion
- LLM
- LlamaIndex
---

# ∞ PDF
## Documentation
- Class name: `LLMPDFReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMPDFReader node is designed to read PDF files and convert them into a format that can be indexed and processed by the llama_index system, focusing on text extraction while currently not supporting embedded images.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the PDF file to be read, serving as the primary input for the document extraction process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in JSON string format that can be used to influence the reading process or provide context for the document extraction.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns the extracted document data from the PDF file, formatted for compatibility with the llama_index system.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMPDFReader(PDFReader):
    """
    @NOTE: Reads PDF files into a llama_index Document, currently doesn't support embedded images
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/docs/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.PDFReader
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
