---
tags:
- LLM
- LlamaIndex
---

# ∞ Paged CSV
## Documentation
- Class name: `LLMPagedCSVReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMPagedCSVReader node specializes in reading CSV files with pagination support, transforming them into a structured llama_index Document list. This node is designed to handle large CSV files efficiently by processing them in pages, thereby facilitating the handling and analysis of big data sets in a manageable way.
## Input types
### Required
- **`path`**
    - Specifies the file path to the CSV file to be read. This parameter is crucial as it determines the source file for data extraction.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`encoding`**
    - Defines the character encoding of the CSV file, such as UTF-8, to ensure correct reading of the file's content.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`delimiter`**
    - Sets the character used to separate columns in the CSV file, allowing for accurate parsing of the data.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`quotechar`**
    - Indicates the character used to wrap text fields in the CSV file, ensuring proper handling of commas or specified delimiters within data fields.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Allows for the inclusion of additional, optional information that can be used during the reading process, offering flexibility in data handling.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - Returns a list of llama_index Documents, each representing a page of data from the CSV file, structured for further processing or analysis.
    - Python dtype: `List[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMPagedCSVReader(PagedCSVReader):
    """
    @NOTE: Reads CSV files into a llama_index Document list, with paging
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/paged_csv/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.PagedCSVReader
    """

    def __init__(self):
        super().__init__()

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "path": ("STRING", {"default": ""}),
                "encoding": ([
                    "utf-8"
                ],),
                "delimiter": ("STRING", { "default": ","}),
                "quotechar": ("STRING", { "default": '"'}),
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, encoding:str, delimiter:str, quotechar:str):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        self._encoding = encoding
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info, delimiter, quotechar)
        return (data, )

```
