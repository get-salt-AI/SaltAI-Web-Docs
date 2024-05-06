---
tags:
- LLM
- LlamaIndex
---

# ∞ Image Tabular Chart
## Documentation
- Class name: `LLMImageTabularChartReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMImageTabularChartReader node is designed to interpret image charts as if they were tabular data, converting visual chart representations into a structured llama_index Document format. This enables the extraction and analysis of data from image-based charts, facilitating their integration into data processing pipelines.
## Input types
### Required
- **`path`**
    - Specifies the file path to the image chart that needs to be read. It is essential for locating the file and initiating the reading process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`max_output_tokens`**
    - Defines the maximum number of output tokens to be generated. This parameter controls the verbosity of the output, affecting the detail level of the extracted data.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`prompt`**
    - Provides a custom prompt to guide the interpretation of the image chart. This can influence the focus and context of the data extraction process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`extra_info`**
    - Allows for the inclusion of additional information in a JSON string format, which can be used to provide context or specify processing options for the image chart reading.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed data from the image chart, structured as a llama_index Document. This output facilitates further analysis or integration into data processing workflows.
    - Python dtype: `Document`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMImageTabularChartReader(ImageTabularChartReader):
    """
    @NOTE: Reads an Image chart as if it were tabular data into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/image_deplot/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.ImageTabularChartReader
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
    #			"keep_image": ([False, True], {"default": False}),
                "max_output_tokens": ("INT", {"default": 512}),
                "prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": ""}),
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "execute"
    CATEGORY = "SALT/Llama-Index/Readers"

    def execute(self, path:str, max_output_tokens:int=512, prompt:str=None, extra_info:str="{}"):
        get_full_path(1, path)
        if not os.path.exists(path):
            raise FileNotFoundError(f"No file available at: {path}")
        path = Path(path)
        self._max_output_tokens = max_output_tokens
        if prompt and len(prompt) > 2:
            self._prompt = prompt
        extra_info = read_extra_info(extra_info)
        data = self.load_data(path, extra_info)
        return (data, )

```
