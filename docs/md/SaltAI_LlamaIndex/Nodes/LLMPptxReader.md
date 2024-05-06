---
tags:
- LLM
- LlamaIndex
---

# ∞ Pptx
## Documentation
- Class name: `LLMPptxReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMPptxReader node specializes in reading Microsoft PowerPoint files and converting them into a format compatible with the llama_index Document structure. It aims to facilitate the extraction of textual content from presentations, although it's not explicitly stated whether images within the slides are interpreted.
## Input types
### Required
- **`path`**
    - Specifies the file system path to the PowerPoint file to be read. It is crucial for locating and accessing the file for content extraction.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information that can be used to influence the reading process, such as specifying particular aspects of the file to focus on or ignore.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output is a llama_index Document representation of the PowerPoint file's content, structured for further processing or analysis.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMPptxReader(PptxReader):
    """
    @NOTE: Reads MS Powerpoint files into a llama_index Document, not sure if images are interpreted
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/slides/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.PptxReader
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
