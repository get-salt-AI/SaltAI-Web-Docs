---
tags:
- LLM
---

# ∞ Tavily Research
## Documentation
- Class name: `LLMTavilyResearch`
- Category: `SALT/Llama-Index/Tools`
- Output node: `False`

The LLMTavilyResearch node is designed to leverage the Tavily API for conducting in-depth research queries. It abstracts the complexity of interfacing with the Tavily API, allowing users to perform advanced search operations, including filtering by domains, excluding specific domains, and adjusting search depth and result count. This node is particularly useful for gathering and analyzing data from a wide range of internet sources, making it an essential tool for research and data collection tasks.
## Input types
### Required
- **`tavily_api_key`**
    - The API key required to authenticate requests to the Tavily service. It enables the node to access Tavily's API for executing search queries and retrieving data.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`search_query`**
    - The search query string used to define the scope and focus of the search performed by the Tavily API. It determines the relevance and specificity of the search results returned.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`search_depth`**
    - Specifies the depth of the search, affecting how extensively the Tavily API explores internet sources to fulfill the query. A higher depth can lead to more comprehensive search results.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`max_results`**
    - The maximum number of search results to retrieve. This limit helps manage the volume of data returned by the search, allowing for more focused analysis.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`include_answer`**
    - Determines whether the search results should include a direct answer to the query, providing a concise response alongside the broader search results.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`include_raw_content`**
    - A boolean flag that, when set, includes the raw content of the search results, offering unprocessed data for further analysis.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`include_domains`**
    - A comma-separated list of domains to specifically include in the search results, allowing for targeted research within certain web domains.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`exclude_domains`**
    - A comma-separated list of domains to exclude from the search results, helping to refine the search by removing unwanted sources.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`keep_looking_limit`**
    - An integer specifying how many additional results to fetch if the initial search does not yield traversable content. This parameter helps in ensuring that the search returns usable results.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - A collection of documents derived from the search results, each containing text and potentially additional information extracted from the search.
    - Python dtype: `List[Document]`
- **`urls`**
    - Comfy dtype: `LIST`
    - A list of URLs extracted from the search results, providing direct links to the sources of the documents.
    - Python dtype: `List[str]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMTavilyResearch:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "tavily_api_key": ("STRING", {"default": "tvly-*******************************"}),
                "search_query": ("STRING", {"multiline": True, "dynamicPrompts": False}),
            },
            "optional": {
                "search_depth": (["basic", "advanced"],),
                "max_results": ("INT", {"min": 1, "max": 20, "default": 1}),
                "include_answer": ("BOOLEAN", {"default": False},),
                "include_raw_content": ("BOOLEAN", {"default": False},),
                "include_domains": ("STRING", {
                    "multiline": True,
                    "dynamicPrompts": False,
                    "placeholder": "A list of domains to specifically include in the search results. Default is None, which includes all domains. e.g. \"google.com, twitter.com\"",
                }),
                "exclude_domains": ("STRING", {
                    "multiline": True,
                    "dynamicPrompts": False,
                    "placeholder": "A list of domains to specifically exclude from the search results. Default is None, which doesn't exclude any domains. e.g. \"google.com, twitter.com\"",
                }),
                "keep_looking_limit": ("INT", {"min": 1, "max": 20, "default": 10})
            }
        }
    
    RETURN_TYPES = ("DOCUMENT", "LIST")
    RETURN_NAMES = ("documents", "urls")

    FUNCTION = "search"
    CATEGORY = "SALT/Llama-Index/Tools"

    def search(self, tavily_api_key, search_query, search_depth="basic", max_results=1, include_answer=False, include_raw_content=False, include_domains="google.com", exclude_domains=None, keep_looking_limit=10):
        
        tavily = TavilyClient(api_key=tavily_api_key)

        def tavily_search():
            return tavily.search(
                query=search_query,
                search_depth=search_depth,
                max_results=max_results,
                include_images=False,
                include_answer=include_answer,
                include_raw_content=include_raw_content,
                include_domains=include_domains.split(
                    ", ") if include_domains is not None and include_domains != "" else None,
                exclude_domains=exclude_domains.split(
                    ", ") if include_domains is not None and exclude_domains != "" else None,
            )
        
        print("Tavily Search Query:", search_query)

        # Increment the search results because when using `include_raw_content` 
        # results are found in order of accessibility, so first X results may not 
        # be traversible, and end up in there being no result to return. But maybe 
        # the next search result does have traversible content.
        adjusted_max_results = max_results + keep_looking_limit
        current_retry = 0
        response = {}
        while "results" not in response or not response["results"] and max_results < adjusted_max_results:
                max_results += 1
                if current_retry > 0:
                    print(f"Unable find any results. Continuing Search...\nRetry {current_retry} of {keep_looking_limit}")
                response = tavily_search()
                current_retry += 1

        pprint(response, indent=4)

        results = response.get("results", None)
        urls = []
        documents = []
        if results:
            for result in results:
                content = result.pop("content", "null")
                raw_content = result.pop("raw_content", None)
                document = Document(
                    text=(raw_content if raw_content else content),
                    extra_info=result
                )
                documents.append(document)
                if result.__contains__("url"):
                    urls.append(result["url"])
        else:
            documents.append(Document(text="No document data available", extra_info={"error": "No document data available"}))
            urls.append(None)

        return (documents, urls)

```
