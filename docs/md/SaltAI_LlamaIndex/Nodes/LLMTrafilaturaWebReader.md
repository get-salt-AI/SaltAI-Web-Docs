---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ Trafilatura Web
## Documentation
- Class name: `LLMTrafilaturaWebReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMTrafilaturaWebReader node is designed to extract content from web pages using the Trafilatura library. It processes up to four URLs to retrieve and parse web page documents, facilitating the extraction of clean text for further analysis or processing.
## Input types
### Required
- **`url_i`**
    - A URL from which to extract content. Multiple URLs (up to four) can be specified to extract content from multiple web pages simultaneously. Each URL is a starting point for content extraction, contributing to the node's capability to process multiple web pages in parallel.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The extracted content from the provided URLs, returned as documents for further processing or analysis.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMTrafilaturaWebReader:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "url_1": ("STRING", {}),
            },
            "optional": {
                "url_2": ("STRING", {}),
                "url_3": ("STRING", {}),
                "url_4": ("STRING", {}),
            },
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "read_web_trafilatura"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_web_trafilatura(self, url_1, url_2=None, url_3=None, url_4=None):
        if not url_1.strip():
            raise ValueError("At least one URL must be provided to LLMTrafilaturaWebReader")

        urls = [url_1.strip()]
        if url_2.strip():
            urls.append(url_2.strip())
        if url_3.strip():
            urls.append(url_3.strip())
        if url_4.strip():
            urls.append(url_4.strip())

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
