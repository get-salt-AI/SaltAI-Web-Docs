---
tags:
- LLM
- LlamaIndex
- WebContentExtraction
---

# ∞ Web Crawler
## Documentation
- Class name: `LLMSaltWebCrawler`
- Category: `SALT/Llama-Index/Tools`
- Output node: `False`

The LLMSaltWebCrawler node is designed for web crawling and content extraction, tailored for indexing and analyzing web pages. It leverages a comprehensive crawling strategy to navigate through web links, respecting site's robots.txt rules, and applies keyword filtering to ensure relevancy of the content collected. This node is capable of deep web traversal, extracting textual content and metadata from web pages, and organizing the data into a structured format suitable for further analysis or indexing.
## Input types
### Required
### Optional
- **`url`**
    - The starting point URL for the web crawl. It is crucial as it defines the initial scope of the crawl and influences the breadth and depth of the content that will be explored.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`urls`**
    - A list of URLs to be crawled. This allows for a broader or more targeted crawl, depending on the specific URLs provided, enabling the node to cover multiple starting points or specific areas of interest.
    - Comfy dtype: `LIST`
    - Python dtype: `list`
- **`max_depth`**
    - Defines how deep the crawler should navigate from the starting URL(s). It limits the crawl depth to prevent excessive resource consumption and to focus the crawl on more relevant content layers.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`max_links`**
    - Limits the number of links to follow per page, controlling the breadth of the crawl and managing the load on both the crawler and the target websites.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`trim_line_breaks`**
    - A flag to indicate whether to remove line breaks from the extracted text, which can help in cleaning and preprocessing the text for analysis or storage.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`verify_ssl`**
    - Determines whether to enforce SSL certificate verification when making HTTP requests, affecting the crawler's ability to fetch content from secure sites.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`exclude_domains`**
    - A list of domains to exclude from the crawl, allowing for more focused crawling by avoiding irrelevant or unwanted websites.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`keywords`**
    - Keywords to filter the crawled content, ensuring that only relevant information is collected and processed. This is particularly useful for targeted crawling and data collection.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The output is a list of documents, each containing the extracted content and metadata from crawled web pages. This structured data is ready for indexing, analysis, or integration into larger language models or databases.
    - Python dtype: `list`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSaltWebCrawler:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {},
            "optional": {
                "url": ("STRING", {}),
                "urls": ("LIST", {}),
                "max_depth": ("INT", {"min": 1, "max": 4, "default": 2}),
                "max_links": ("INT", {"min": 1, "max": 6, "default": 2}),
                "trim_line_breaks": ("BOOLEAN", {"default": True}),
                "verify_ssl": ("BOOLEAN", {"default": True}),
                "exclude_domains": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Optional exclude domains, eg: example.com, example2.com"}),
                "keywords": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Optional relevancy keywords, eg: artificial intelligence, ai"})
            }
        }
    

    RETURN_TYPES = ("DOCUMENT",)
    RETURN_NAMES = ("documents",)

    FUNCTION = "crawl"
    CATEGORY = "SALT/Llama-Index/Tools"

    def crawl(self, url:str="google.com", urls:list=None, max_depth:int=2, max_links:int=2, trim_line_breaks:bool=True, verify_ssl:bool=True, exclude_domains:str="", keywords:str="") -> list:

        search_urls = []

        print(urls)

        if not url.strip() and not urls:
            raise Exception("Please provide at lease one URL")
        
        url = url.strip()
        if url != "" and valid_url(url):
            search_urls.append(url)
        if urls:
            search_urls.extend([url for url in urls if valid_url(url)])

        print(search_urls)

        crawler = WebCrawler(search_urls, exclude_domains=exclude_domains, relevancy_keywords=keywords, max_links=max_links)

        results = crawler.parse_sites(crawl=True, max_depth=max_depth, trim_line_breaks=trim_line_breaks, verify_ssl=verify_ssl)

        from pprint import pprint
        pprint(results.to_dict(), indent=4)

        return (results.to_documents(), )

```
