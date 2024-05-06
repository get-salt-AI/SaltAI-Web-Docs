---
tags:
- LLM
---

# ∞ Summary Index
## Documentation
- Class name: `LLMSummaryIndex`
- Category: `SALT/Llama-Index/Indexing`
- Output node: `False`

The LLMSummaryIndex node is designed for creating a summary index from documents using a specified language model. It leverages the model's embedding capabilities to generate a concise representation of the documents, facilitating efficient indexing and retrieval.
## Input types
### Required
- **`llm_model`**
    - The language model used for embedding the documents. It is crucial for determining how documents are represented and indexed.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`document`**
    - The document(s) to be indexed. This input is essential for generating the index based on the content of the documents.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `List[Document]`
### Optional
- **`optional_llm_context`**
    - An optional context provided to the language model during the embedding process. It can influence the embedding results.
    - Comfy dtype: `LLM_CONTEXT`
    - Python dtype: `Optional[Dict[str, Any]]`
## Output types
- **`llm_index`**
    - Comfy dtype: `LLM_INDEX`
    - The generated summary index of the documents. It represents a structured and efficient way to access the document contents based on their embeddings.
    - Python dtype: `SummaryIndex`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSummaryIndex:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "document": ("DOCUMENT",),
            },
            "optional": {
                "optional_llm_context": ("LLM_CONTEXT",),
            },
        }

    RETURN_TYPES = ("LLM_INDEX",)
    RETURN_NAMES = ("llm_index",)

    FUNCTION = "index"
    CATEGORY = "SALT/Llama-Index/Indexing"

    def index(self, llm_model, document, optional_llm_context=None):
        index = SummaryIndex.from_documents(document, embed_model=llm_model['embed_model'], service_context=optional_llm_context or None)
        return (index,)

```
