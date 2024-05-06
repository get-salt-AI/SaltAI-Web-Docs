---
tags:
- LLM
- LlamaIndex
---

# ∞ Pandas CSV
## Documentation
- Class name: `LLMPandasCSVReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMPandasCSVReader node specializes in reading CSV files and transforming them into a structured llama_index Document format, with additional configuration options for concatenating rows and customizing how columns and rows are joined.
## Input types
### Required
- **`path`**
    - Specifies the file path of the CSV to be read. It is crucial for locating and accessing the target CSV file.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`concat_rows`**
    - Determines whether rows should be concatenated. This affects how the CSV data is processed and structured into the document.
    - Comfy dtype: `COMBO[BOOLEAN]`
    - Python dtype: `bool`
- **`col_joiner`**
    - Defines the string used to join columns when concatenating rows, influencing the final document structure.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`row_joiner`**
    - Specifies the string used to join rows, impacting the overall formatting and structure of the resulting document.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Allows for the inclusion of additional, custom information that can be utilized during the reading process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed CSV data, structured as a llama_index Document.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMPandasCSVReader(PandasCSVReader):
    """
    @NOTE: Reads CSV files into a llama_index Document, with some additional joiner config
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/tabular/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.PandasCSVReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
                "concat_rows": ([False, True], {"default": True}),
                "col_joiner": ("STRING", {"default":""}),
                "row_joiner": ("STRING", {"default":""}),
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
    			#"pandas_config": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path, concat_rows, col_joiner, row_joiner, extra_info:str="{}", fs = None):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        self._concat_rows=concat_rows
        self._col_joiner=col_joiner
        self._row_joiner=row_joiner
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
