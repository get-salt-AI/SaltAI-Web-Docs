---
tags:
- LLM
---

# ∞ Tavily Search Tool
## Documentation
- Class name: `CreateTavilySearchTool`
- Category: `SALT/Llama-Index/Agents`
- Output node: `False`

This node facilitates the creation of a search tool powered by the Tavily API, designed to perform comprehensive searches across the web. It allows for the customization of search parameters such as depth, result limits, and domain filtering to tailor the search results according to specific needs.
## Input types
### Required
- **`api_key`**
    - The API key required to authenticate requests with the Tavily API, enabling access to its search functionalities.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`function_name`**
    - The name assigned to the created search tool, serving as an identifier for its usage.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`search_depth`**
    - Determines the thoroughness of the search, allowing users to specify how extensively the search engine should look for results.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`max_results`**
    - Limits the number of search results returned, enabling control over the volume of data to process.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`include_answer`**
    - A flag to include a direct answer within the search results, providing succinct responses to queries.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`include_raw_content`**
    - A flag to include the raw content of search results, offering access to unprocessed data.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
### Optional
- **`include_domains`**
    - Specifies a list of domains to include in the search results, allowing for targeted searches within specific websites.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`exclude_domains`**
    - Specifies a list of domains to exclude from the search results, enabling the omission of content from specific websites.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`tool`**
    - Comfy dtype: `TOOL`
    - The search tool configured with the specified parameters, ready for integration and use in various applications.
    - Python dtype: `Tuple[Dict[str, Any]]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class CreateTavilySearchTool:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "api_key": ("STRING", {"multiline": False, "dynamicPrompts": False, "placeholder": "Tavily API key"}),
                "function_name": ("STRING", {"multiline": False, "dynamicPrompts": False, "default": "tavily_search_tool"}),
                "search_depth": (["basic", "advanced"],),
                "max_results": ("INT", {"default": 5}),
                "include_answer": ("BOOLEAN", {"default": False},),
                "include_raw_content": ("BOOLEAN", {"default": False},),
            },
            "optional": {
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
            }
        }

    RETURN_TYPES = ("TOOL",)
    RETURN_NAMES = ("tool",)

    FUNCTION = "create_tool"
    CATEGORY = "SALT/Llama-Index/Agents"

    def create_tool(
        self,
        api_key,
        function_name,
        search_depth,
        max_results,
        include_answer,
        include_raw_content,
        include_domains=None,
        exclude_domains=None
    ):
        tavily = TavilyClient(api_key=api_key)

        def search(query: str):
            response = tavily.search(
                query=query,
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
            context = [obj["content"] for obj in response['results']]
            return "/n".join(context)

        tool = {
            "name": function_name,
            "description": "A search engine optimized for comprehensive, accurate, and trusted results. Useful for when you need to answer questions about current events. Input should be a search query.",
            "function": search,
        }
        return (tool,)

```
