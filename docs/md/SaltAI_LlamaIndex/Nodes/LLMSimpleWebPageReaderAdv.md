---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ Simple Web Page (Advanced)
## Documentation
- Class name: `LLMSimpleWebPageReaderAdv`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMSimpleWebPageReaderAdv node is designed to fetch and process web pages from a list of URLs, converting them into a structured document format. It optionally converts HTML content to text, facilitating the extraction of textual information from web pages for further analysis or processing.
## Input types
### Required
- **`urls`**
    - A list of URLs from which the web pages will be fetched. This is the primary input that drives the node's operation, determining the source of the web content to be processed.
    - Comfy dtype: `LIST`
    - Python dtype: `List[str]`
### Optional
- **`html_to_text`**
    - A boolean flag indicating whether the HTML content should be converted to plain text. This affects how the web page content is processed and represented in the output documents.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The processed web page content, structured as documents. This output encapsulates the textual information extracted from the provided web pages, ready for further analysis or processing.
    - Python dtype: `Tuple[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSimpleWebPageReaderAdv:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "urls": ("LIST", {}),
            },
            "optional": {
                "html_to_text": ("BOOLEAN", {"default": True}),
            },
        }

    RETURN_TYPES = ("DOCUMENT", )
    RETURN_NAMES = ("documents",)

    FUNCTION = "read_web"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_web(self, urls, html_to_text=True):

        if not urls:
            raise ValueError("At least one URL must be provided to LLMSimpleWebPageReaderAdv")

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
