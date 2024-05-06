---
tags:
- LLM
- LlamaIndex
---

# ∞ Unstructured File
## Documentation
- Class name: `LLMUnstructuredReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMUnstructuredReader node is designed to read and process unstructured files, converting them into a format suitable for integration with the llama_index Document structure. This node supports a wide range of file types, making it versatile for various unstructured data processing tasks.
## Input types
### Required
- **`path`**
    - Specifies the file path to the unstructured file to be read. This is crucial for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`split_documents`**
    - A boolean flag indicating whether to split the input file into multiple documents based on certain criteria. This affects how the file is processed and segmented.
    - Comfy dtype: `COMBO[BOOLEAN]`
    - Python dtype: `bool`
### Optional
- **`extra_info`**
    - Provides additional information or metadata in a string format that can be associated with the document(s) generated from the file. This allows for more customized processing and output.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed output from the unstructured file, structured as a llama_index Document. This output is ready for further processing or integration within the llama_index ecosystem.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMUnstructuredReader(UnstructuredReader):
    """
    @NOTE: Reads unstructured (most kinds of) files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/unstructured/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.UnstructuredReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
                "split_documents": ([False, True], { "default": False})
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, extra_info:str, split_documents:bool = False):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info, split_documents)
        return (data, )

```
