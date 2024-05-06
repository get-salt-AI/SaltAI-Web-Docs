---
tags:
- LLM
- LlamaIndex
---

# ∞ Video/Audio
## Documentation
- Class name: `LLMVideoAudioReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMVideoAudioReader node is designed to read and process video and audio files, converting their audio content into a structured document format suitable for indexing and analysis.
## Input types
### Required
- **`path`**
    - Specifies the file path of the video or audio file to be processed. It is essential for locating and accessing the file for processing.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`extra_info`**
    - Provides additional, optional information that can be used to influence the processing of the video or audio file.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed audio content from the video or audio file, structured as a document for indexing and analysis.
    - Python dtype: `tuple[dict]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMVideoAudioReader(VideoAudioReader):
    """
    @NOTE: Reads Mp3 and Mp4 files and parses audio transcript into a llama_index Document
    @Source: https://github.com/run-llama/llama_index/blob/main/llama-index-integrations/readers/llama-index-readers-file/llama_index/readers/file/video_audio/base.py
    @Documentation: https://docs.llamaindex.ai/en/latest/api_reference/readers/file/#llama_index.readers.file.VideoAudioReader
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
