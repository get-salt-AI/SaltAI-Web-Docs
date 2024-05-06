---
tags:
- LLM
---

# ∞ Semantics Splitter Node Parser
## Documentation
- Class name: `LLMSemanticSplitterNodeParser`
- Category: `SALT/Llama-Index/Parsing`
- Output node: `False`

The LLMSemanticSplitterNodeParser node is designed to parse documents into semantic nodes using a specified language model for embeddings, optionally incorporating sentence splitting, metadata inclusion, and relationship tracking between nodes. It aims to enhance document understanding by structuring content into semantically meaningful units.
## Input types
### Required
- **`document`**
    - The document to be parsed into semantic nodes. It serves as the primary input for semantic analysis and node creation.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `str`
- **`llm_embed_model`**
    - The language model used for generating embeddings, which underpins the semantic parsing process.
    - Comfy dtype: `LLM_EMBED_MODEL`
    - Python dtype: `object`
### Optional
- **`buffer_size`**
    - Specifies the number of documents to process in parallel, affecting performance and resource utilization.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`sentence_splitter`**
    - An optional component that determines how sentences are split within the document, influencing the granularity of semantic nodes.
    - Comfy dtype: `LLM_SENTENCE_SPLITTER`
    - Python dtype: `object`
- **`include_metadata`**
    - Controls whether metadata is included in the semantic nodes, enriching the nodes with additional context.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`include_prev_next_rel`**
    - Determines whether relationships between consecutive nodes are tracked, facilitating the understanding of document flow.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`llm_node_parser`**
    - Comfy dtype: `LLM_NODE_PARSER`
    - The result of the semantic parsing process, comprising a structured representation of the document in the form of semantic nodes.
    - Python dtype: `object`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMSemanticSplitterNodeParser:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "document": ("DOCUMENT",),
                "llm_embed_model": ("LLM_EMBED_MODEL",),
            },
            "optional": {
                "buffer_size": ("INT", {"default": 1, "min": 1}),
                "sentence_splitter": ("LLM_SENTENCE_SPLITTER",),
                "include_metadata": ("BOOLEAN", {"default": True}),
                "include_prev_next_rel": ("BOOLEAN", {"default": True}),
            },
        }

    RETURN_TYPES = ("LLM_NODE_PARSER",)
    RETURN_NAMES = ("llm_node_parser",)

    FUNCTION = "semantic_nodes"
    CATEGORY = "SALT/Llama-Index/Parsing"

    def semantic_nodes(self, document, llm_embed_model, buffer_size=1, sentence_splitter=None, include_metadata=True, include_prev_next_rel=True):
        parser = SemanticSplitterNodeParser(
            embed_model=llm_embed_model,
            buffer_size=buffer_size,
            sentence_splitter=sentence_splitter,
            include_metadata=include_metadata,
            include_prev_next_rel=include_prev_next_rel,
        )
        return (parser.build_semantic_nodes_from_documents(document, show_progress=True), )

```
