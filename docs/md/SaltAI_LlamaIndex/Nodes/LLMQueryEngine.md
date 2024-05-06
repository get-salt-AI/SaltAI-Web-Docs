---
tags:
- LLM
---

# ∞ Query Engine
## Documentation
- Class name: `LLMQueryEngine`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

The LLMQueryEngine node is designed to facilitate querying within a document or a set of documents using a language model and an index. It abstracts the complexity of querying processes, enabling users to retrieve relevant information or answers based on their input queries.
## Input types
### Required
- **`llm_model`**
    - The language model used for processing queries. It plays a crucial role in understanding and generating responses based on the input query.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`llm_index`**
    - The index that organizes and manages the documents to be queried. It is essential for efficiently retrieving relevant documents based on the query.
    - Comfy dtype: `LLM_INDEX`
    - Python dtype: `Dict[str, Any]`
### Optional
- **`query`**
    - The user's input query. It is optional and used to specify the information or answer being sought.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`llm_message`**
    - A list of messages that can be used to provide additional context or information for the query processing.
    - Comfy dtype: `LIST`
    - Python dtype: `List[Any]`
## Output types
- **`results`**
    - Comfy dtype: `STRING`
    - The output of the query process, typically containing the information or answer retrieved from the documents based on the input query.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMQueryEngine:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
                "llm_model": ("LLM_MODEL", ),
				"llm_index": ("LLM_INDEX", ),
			},
			"optional": {
				"query": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Type your query here"}),
				"llm_message": ("LIST", {}),
			}
		}

	RETURN_TYPES = ("STRING",)
	RETURN_NAMES = ("results",)

	FUNCTION = "query_engine"
	CATEGORY = "SALT/Llama-Index/Querying"

	def query_engine(self, llm_model, llm_index, query=None, llm_message=None):
		query_components = []
		
		if llm_message and isinstance(llm_message, list):
			for msg in llm_message:
				if str(msg).strip():
					query_components.append(str(msg))
		else:
			query_components.append("Analyze the above document carefully to find your answer. If you can't find one, say so.")

		if query:
			if query.strip():
				query_components.append("user: " + query)
		query_components.append("assistant:")

		pprint(query_components, indent=4)

		query_join = "\n".join(query_components)

		query_engine = llm_index.as_query_engine(llm=llm_model.get("llm", None), embed_model=llm_model.get("embed_model", None))
		response = query_engine.query(query_join)
		pprint(response, indent=4)
		return (response.response,)

```
