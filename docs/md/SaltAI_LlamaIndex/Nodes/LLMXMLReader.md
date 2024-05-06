---
tags:
- LLM
- LlamaIndex
---

# ∞ XML
## Documentation
- Class name: `LLMXMLReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMXMLReader node specializes in reading XML files and converting them into a format compatible with the llama_index Document structure. It leverages the XMLReader base class to parse XML files, facilitating the integration of XML data into the llama_index ecosystem for further processing or analysis.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the XML file to be read. This path is essential for locating and accessing the file for parsing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information in a string format that can be used to influence the reading process. This could include configuration details or metadata to enhance the parsing operation.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns a llama_index Document object derived from the parsed XML file, ready for integration into the llama_index ecosystem.
    - Python dtype: `tuple[llama_index.Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMXMLReader(XMLReader):
    """
    @NOTE: Reads XML files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/xml/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.XMLReader
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
