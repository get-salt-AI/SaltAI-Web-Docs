---
tags:
- LLM
---

# ∞ Vector Store Index
## Documentation
- Class name: `LLMVectorStoreIndex`
- Category: `SALT/Llama-Index/Indexing`
- Output node: `False`

The LLMVectorStoreIndex node is designed for indexing documents using a language model to create a vector store index. This process involves transforming documents into a vector space representation, leveraging the embedding capabilities of a specified language model, to facilitate efficient retrieval and similarity searches.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to use for document embedding, which is crucial for the indexing process as it determines the vector space in which documents will be represented.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`document`**
    - The document(s) to be indexed. This input is essential for creating the vector store index by converting the document content into vector representations.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `Sequence[Document]`
### Optional
- **`optional_llm_context`**
    - An optional context for the language model that can be used to tailor the embedding process, enhancing the relevance of the generated index.
    - Comfy dtype: `LLM_CONTEXT`
    - Python dtype: `Dict[str, Any]`
## Output types
- **`llm_index`**
    - Comfy dtype: `LLM_INDEX`
    - The generated vector store index, which enables efficient retrieval and similarity searches among the indexed documents.
    - Python dtype: `VectorStoreIndex`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMVectorStoreIndex:
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

    def index(self, llm_model, document, optional_llm_context = None):
        
        #document = cast(Sequence[Document], document) # This could be why documents are not working correctly
        embed_model = llm_model.get("embed_model", None)
        
        if not embed_model:
            raise ValueError("Unable to determine LLM Embedding Model")
        
        index = VectorStoreIndex.from_documents(document, embed_model=embed_model, service_context=optional_llm_context)
        return (index,)

```
