---
tags:
- LLM
---

# ∞ Query Engine As Tool
## Documentation
- Class name: `LLMQueryEngineAsTool`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

This node encapsulates a query engine tool designed to interact with documents by processing queries to find and generate relevant information or answers from the document content.
## Input types
### Required
- **`name`**
    - A unique identifier for the tool, typically used to name the query engine instance.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`description`**
    - A detailed description of the tool's purpose and functionality, guiding users on how it can be utilized to interact with documents.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`llm_index`**
    - Represents the index or database against which the query will be executed, enabling the tool to search and retrieve information.
    - Comfy dtype: `LLM_INDEX`
    - Python dtype: `custom type`
## Output types
- **`query_tool`**
    - Comfy dtype: `TOOL`
    - The constructed query tool capable of processing and answering queries based on the document content.
    - Python dtype: `dict`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMQueryEngineAsTool:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
				"name": ("STRING", {"multiline": False, "dynamicPrompts": False, "placeholder": "code"}),
				"description": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "A function that allows you to communicate with a document. Ask a question and this function will find information in the document and generate an answer."}),
				"llm_index": ("LLM_INDEX",),
			},
		}

	RETURN_TYPES = ("TOOL",)
	RETURN_NAMES = ("query_tool",)

	FUNCTION = "return_tool"
	CATEGORY = "SALT/Llama-Index/Querying"
	
	def return_tool(self, name, description, llm_index):
		def query_engine(query: str) -> str:
			query_components = []
			query_components.append("Analyze the above document carefully to find your answer. If you can't find one, say so.")

			if query:
				if query.strip():
					query_components.append("user: " + query)
			query_components.append("assistant:")
			pprint(query_components, indent=4)
			query_join = "\n".join(query_components)

			query_engine = llm_index.as_query_engine()
			response = query_engine.query(query_join)
			pprint(response, indent=4)
			return (response.response,)
		tool = {"name": name, "description": description, "function": query_engine}
		return (tool,)

```
