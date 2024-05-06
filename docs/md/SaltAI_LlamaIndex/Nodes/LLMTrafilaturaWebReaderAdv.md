---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ Trafilatura Web (Advanced)
## Documentation
- Class name: `LLMTrafilaturaWebReaderAdv`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

This node is designed for extracting and processing web content from multiple URLs using the Trafilatura library, enabling efficient web scraping and content extraction for further analysis or processing.
## Input types
### Required
- **`urls`**
    - A list of URLs from which the web content will be extracted. This parameter is essential for the node's operation as it defines the sources of the web content to be processed.
    - Comfy dtype: `LIST`
    - Python dtype: `List[str]`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The extracted web content from the specified URLs, processed and returned as documents.
    - Python dtype: `Tuple[List[str]]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMTrafilaturaWebReaderAdv:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "urls": ("LIST", {}),
            },
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "read_web_trafilatura"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_web_trafilatura(self, urls):

        if not urls:
            raise ValueError("At least one URL must be provided to LLMTrafilaturaWebReaderAdv")

        valid_urls = []
        for url in urls:
            if not valid_url(url):
                print("Skipping invalid URL", url)
                continue
            valid_urls.append(url)

        print("Valided URLs:", valid_urls)

        documents = TrafilaturaWebReader().load_data(valid_urls)
        return (documents,)

```
