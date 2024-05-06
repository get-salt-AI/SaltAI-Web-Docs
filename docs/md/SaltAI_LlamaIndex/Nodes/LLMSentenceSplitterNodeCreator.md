---
tags:
- LLM
---

# ∞ Setence Splitter Node Creator
## Documentation
- Class name: `LLMSentenceSplitterNodeCreator`
- Category: `SALT/Llama-Index/Tools`
- Output node: `False`

The LLMSentenceSplitterNodeCreator is designed to segment a given document into smaller, manageable nodes based on sentence boundaries. This process facilitates the handling and analysis of large texts by breaking them down into more digestible pieces.
## Input types
### Required
- **`document`**
    - The 'document' parameter is the primary text input that the node will segment into smaller nodes. It is essential for defining the scope of text to be analyzed and split.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `str`
### Optional
- **`chunk_size`**
    - The 'chunk_size' parameter determines the maximum size of each text chunk or node, affecting how the document is segmented.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`chunk_overlap`**
    - The 'chunk_overlap' parameter specifies the number of characters that adjacent chunks will overlap, ensuring continuity and context preservation between segments.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`llm_nodes`**
    - Comfy dtype: `LLM_NODES`
    - The output 'llm_nodes' consists of the segmented portions of the input document, structured as manageable nodes for further processing.
    - Python dtype: `List[str]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSentenceSplitterNodeCreator:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "document": ("DOCUMENT",),
            },
            "optional": {
                "chunk_size": ("INT", {"default": 1024, "min": 1}),
                "chunk_overlap": ("INT", {"default": 20, "min": 0}),
            },
        }

    RETURN_TYPES = ("LLM_NODES",)
    RETURN_NAMES = ("llm_nodes",)

    FUNCTION = "create_nodes"
    CATEGORY = "SALT/Llama-Index/Tools"

    def create_nodes(self, document, chunk_size=1024, chunk_overlap=20):
        node_parser = SentenceSplitter(chunk_size=chunk_size, chunk_overlap=chunk_overlap)
        nodes = node_parser.get_nodes_from_documents(document, show_progress=False)        
        return (nodes,)

```
