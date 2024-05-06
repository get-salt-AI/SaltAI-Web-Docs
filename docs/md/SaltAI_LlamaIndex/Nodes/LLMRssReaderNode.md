---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ RSS
## Documentation
- Class name: `LLMRssReaderNode`
- Category: `SALT/Llama-Index/Readers`
- Output node: `False`

The LLMRssReaderNode is designed to fetch and parse RSS feeds from provided URLs, converting the content into a structured document format. It supports multiple URLs, allowing for the aggregation of content from various sources into a single, unified collection of documents.
## Input types
### Required
- **`url_i`**
    - A URL from which to read the RSS feed. This input is required to initiate the reading process and can include up to four URLs (url_1 to url_4), allowing for a broader range of content sources.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The structured documents generated from the RSS feeds, ready for further processing or analysis.
    - Python dtype: `Tuple[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMRssReaderNode:
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

    FUNCTION = "read_rss"
    CATEGORY = "SALT/Llama-Index/Readers"

    def read_rss(self, url_1, url_2=None, url_3=None, url_4=None):
        if not url_1.strip():
            raise ValueError("At least one URL must be provided to LLMSimpleWebPageReader")

        urls = [url_1.strip()]
        if url_2.strip():
            urls.append(url_2.strip())
        if url_3.strip():
            urls.append(url_3.strip())
        if url_4.strip():
            urls.append(url_4.strip())

        urls = [url for url in urls if valid_url(url)]

        print("Valided URLs:", urls)

        documents = RssReader().load_data(urls)
        return (documents,)

```
