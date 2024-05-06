---
tags:
- LLM
---

# ∞ Append to Documents List
## Documentation
- Class name: `LLMDocumentListAppend`
- Category: `SALT/Llama-Index/Documents`
- Output node: `False`

The LLMDocumentListAppend node is designed to merge two sets of documents, appending additional documents to an existing collection, potentially enriching them with extra metadata. This functionality is crucial for aggregating and enhancing document datasets within the Llama-Index ecosystem.
## Input types
### Required
- **`llm_documents`**
    - The primary set of documents to which additional documents will be appended. Acts as the base collection for the merge operation.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `List[Document]`
- **`append_llm_documents`**
    - A set of documents to be appended to the primary document collection, allowing for the expansion and enrichment of the dataset.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `List[Document]`
### Optional
- **`extra_info`**
    - Optional JSON-formatted string containing additional metadata to be merged into the metadata of the documents being appended. Enhances the contextual information of the appended documents.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The combined collection of documents after appending the additional documents, potentially enriched with extra metadata.
    - Python dtype: `List[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMDocumentListAppend:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_documents": ("DOCUMENT",),
                "append_llm_documents": ("DOCUMENT",),
            },
            "optional": {
                "extra_info": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "{}"}),
            }
        }

    RETURN_TYPES = ("DOCUMENT",)
    RETURN_NAMES = ("documents",)

    FUNCTION = "document_append"
    CATEGORY = "SALT/Llama-Index/Documents"

    def document_append(self, llm_documents, append_llm_documents, extra_info={}):
        extra_info = json.loads(extra_info)
        for doc in append_llm_documents:
            if isinstance(doc.metadata, dict):
                doc.metadata.update(extra_info)
            elif doc.metadata == None:
                doc.metadata = extra_info
            llm_documents.append(doc)
        return (llm_documents, )

```
