---
tags:
- LLM
---

# ∞ Query Engine (Advanced)
## Documentation
- Class name: `LLMQueryEngineAdv`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

The LLMQueryEngineAdv class is designed to facilitate advanced querying capabilities within a document or a set of documents, leveraging large language models (LLMs) and specialized indexing techniques to extract and synthesize information based on user queries. It aims to provide a more nuanced and context-aware retrieval of information, enhancing the precision and relevance of the responses.
## Input types
### Required
- **`llm_model`**
    - Specifies the large language model to be used for processing queries. It plays a crucial role in understanding and generating responses based on the content of the documents. Its selection directly influences the engine's ability to interpret and respond to queries accurately, impacting the quality of the extracted information.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`llm_index`**
    - Represents the specialized index that organizes and manages the documents for efficient querying. It is essential for quickly locating relevant information within the document corpus. The efficiency and accuracy of the search depend on how well the index is structured and its compatibility with the LLM.
    - Comfy dtype: `LLM_INDEX`
    - Python dtype: `VectorStoreIndex`
### Optional
- **`query`**
    - The user's query as a string. It is the question or request for information that the engine will process to find relevant answers within the documents. The specificity and clarity of the query can significantly affect the relevance of the results obtained.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`llm_message`**
    - A list of messages or prompts that can be used to guide the query processing, providing context or additional information for the query engine. These messages can refine the search, leading to more targeted and appropriate responses.
    - Comfy dtype: `LIST`
    - Python dtype: `List[str]`
- **`top_k`**
    - Determines the number of top results to retrieve based on similarity scores. It influences the breadth of the search, affecting the range and diversity of the responses.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`similarity_cutoff`**
    - Sets the minimum similarity score for results to be considered relevant. This threshold helps in filtering out less pertinent information, ensuring that the responses are closely aligned with the query's intent.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
## Output types
- **`results`**
    - Comfy dtype: `STRING`
    - The output of the query engine, typically a string containing the information retrieved or synthesized in response to the user's query. The nature and quality of these results are directly influenced by the input parameters and the engine's processing capabilities.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMQueryEngineAdv:
	@classmethod
	def INPUT_TYPES(cls):
		return {
			"required": {
                "llm_model": ("LLM_MODEL",),
				"llm_index": ("LLM_INDEX",),
			},
			"optional": {
				"query": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "Type your query here"}),
				"llm_message": ("LIST", {}),
				"top_k": ("INT", {"default": 10}),
				"similarity_cutoff": ("FLOAT", {"default": 0.7}),
			}
		}

	RETURN_TYPES = ("STRING",)
	RETURN_NAMES = ("results",)

	FUNCTION = "query_engine"
	CATEGORY = "SALT/Llama-Index/Querying"

	def query_engine(self, llm_model, llm_index, query=None, llm_message=None, top_k=10, similarity_cutoff=0.7):
		query_components = []
		
		if llm_message and isinstance(llm_message, list):
			for msg in llm_message:
				if str(msg.content).strip():
					query_components.append(str(msg.content))
		else:
			query_components.append("Analyze the above document carefully to find your answer. If you can't find one, say so.")

		if query and query.strip():
			query_components.append("user: " + query)

		query_components.append("assistant:")

		pprint(query_components, indent=4)
		query_join = "\n".join(query_components)

		retriever = VectorIndexRetriever(index=llm_index, similarity_top_k=top_k, embed_model=llm_model.get("embed_model", None))
		query_engine = RetrieverQueryEngine(
			retriever=retriever,
			node_postprocessors=[SimilarityPostprocessor(similarity_cutoff=similarity_cutoff)],
		)

		response = query_engine.query(query_join)
		pprint(response, indent=4)
		return (response.response,)

```
