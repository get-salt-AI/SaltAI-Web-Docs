---
tags:
- LLM
---

# ∞ Post Process Documents
## Documentation
- Class name: `LLMPostProcessDocuments`
- Category: `SALT/Llama-Index/Tools`
- Output node: `False`

This node processes a collection of documents by filtering them based on required and excluded keywords. It aims to refine the document set according to specified keyword criteria, enhancing the relevance of the document collection for further processing or analysis.
## Input types
### Required
- **`document`**
    - The primary collection of documents to be processed. It serves as the input data that will be filtered based on the presence or absence of specified keywords.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `List[Document]`
### Optional
- **`required_keywords`**
    - A comma-separated list of keywords that must be present in a document for it to be included in the output. This parameter allows for the specification of essential content criteria.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`exclude_keywords`**
    - A comma-separated list of keywords that, if present in a document, will result in its exclusion from the output. This parameter enables the omission of documents containing undesired content.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`documents`**
    - Comfy dtype: `DOCUMENT`
    - The filtered collection of documents, refined based on the presence of required keywords and the absence of excluded keywords.
    - Python dtype: `List[Document]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMPostProcessDocuments:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "document": ("DOCUMENT",),
            },
            "optional": {
                "required_keywords": ("STRING", {}),
                "exclude_keywords": ("STRING", {}),
            },
        }

    RETURN_TYPES = ("DOCUMENT",)
    RETURN_NAMES = ("documents",)

    FUNCTION = "process_documents"
    CATEGORY = "SALT/Llama-Index/Tools"

    def process_documents(self, document, required_keywords=[], exclude_keywords=[]):

        if required_keywords.strip():
            required = [ext.strip() for ext in required_keywords.split(",") if ext.strip()]
        else:
            required = None

        if exclude_keywords.strip():
            excluded = [pattern.strip() for pattern in exclude_keywords.split(",") if pattern.strip()]
        else:
            excluded = None

        if required or excluded:
            document = [doc for doc in document if not set(required).isdisjoint(doc.keywords) and set(excluded).isdisjoint(doc.keywords)]

        return (document,)

```
