---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ HTML Tag
## Documentation
- Class name: `LLMHTMLTagReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMHTMLTagReader node specializes in reading HTML tags and converting them into a structured document format, suitable for further processing or analysis. It leverages the BeautifulSoup library to parse HTML content, focusing on specific tags and attributes to extract meaningful data.
## Input types
### Required
- **`path`**
    - Specifies the file path to the HTML document to be read. This is a crucial parameter as it determines the source of the HTML content to be processed.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`tag`**
    - Defines the specific HTML tag to focus on during the parsing process. This allows for targeted extraction of content, enhancing the relevance of the output.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`ignore_no_id`**
    - A boolean flag that, when set, instructs the reader to ignore HTML elements without an ID attribute. This can help in filtering out unnecessary elements.
    - Comfy dtype: `COMBO[BOOLEAN]`
    - Python dtype: `bool`
- **`extra_info`**
    - Allows for the inclusion of additional, custom information in the parsing process, which can be used for further customization or processing steps.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output is a structured document format that encapsulates the extracted data from the HTML content, ready for further analysis or processing.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMHTMLTagReader(HTMLTagReader):
    """
    @NOTE: Reads HTML tags into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/html/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.HTMLTagReader
    @Imports: from bs4 import BeautifulSoup
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
                "tag": ("STRING", {"default":"section"}),
                "ignore_no_id": ([False, True],),
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, tag:str="section", ignore_no_id:bool=False, extra_info:str="{}"):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        self._tag = tag
        self._ignore_no_id = ignore_no_id
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
