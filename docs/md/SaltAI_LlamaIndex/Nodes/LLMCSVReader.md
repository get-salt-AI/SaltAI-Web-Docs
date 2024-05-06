---
tags:
- LLM
- LlamaIndex
---

# ∞ CSV
## Documentation
- Class name: `LLMCSVReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMCSVReader node is designed to read CSV files and convert them into a format compatible with the llama_index Document structure, facilitating the integration of tabular data into the llama_index ecosystem.
## Input types
### Required
- **`path`**
    - Specifies the file path to the CSV file to be read. It is crucial for locating the file and loading its contents.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`concat_rows`**
    - Determines whether rows from the CSV file should be concatenated. This affects how the data is structured in the resulting Document.
    - Comfy dtype: `COMBO[BOOLEAN]`
    - Python dtype: `bool`
### Optional
- **`extra_info`**
    - Allows for the inclusion of additional, user-defined information to be associated with the Document. This can enhance the metadata or context of the data being processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed data from the CSV file, structured as a llama_index Document, ready for further processing or integration.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMCSVReader(CSVReader):
    """
    @NOTE: Reads CSV files into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/tabular/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.CSVReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
                "concat_rows": ([False,True], {"default":True}),
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, concat_rows:bool, extra_info:str):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        self.concat_rows = concat_rows
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
