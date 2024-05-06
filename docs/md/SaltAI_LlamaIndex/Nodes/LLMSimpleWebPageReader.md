---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ Simple Web Page
## Documentation
- Class name: `LLMSimpleWebPageReader`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMSimpleWebPageReader node is designed to fetch and process web pages from specified URLs, converting the HTML content into a text format if desired. This functionality facilitates the extraction of readable content from web pages for further processing or analysis.
## Input types
### Required
- **`url_i`**
    - The primary URL from which the web page will be read. This is the only required URL, indicating the starting point for the web page reading process.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`html_to_text`**
    - A boolean flag indicating whether the HTML content should be converted to plain text, enhancing readability and further processing.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed documents extracted from the web pages, potentially converted to text format.
    - Python dtype: `tuple`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSimpleWebPageReader:
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
                "html_to_text": ("BOOLEAN", {"default": True}),
            },
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "read_web"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_web(self, url_1, url_2=None, url_3=None, url_4=None, html_to_text=True):
        if not url_1.strip():
            raise ValueError("At least one URL must be provided to LLMSimpleWebPageReader")

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

        documents = SimpleWebPageReader(html_to_text=html_to_text).load_data(valid_urls)
        return (documents,)

```
