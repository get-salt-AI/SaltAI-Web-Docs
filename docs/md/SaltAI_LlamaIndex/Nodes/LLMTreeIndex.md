---
tags:
- LLM
---

# ∞ Tree Index
## Documentation
- Class name: `LLMTreeIndex`
- Category: `SALT/Llama-Index/Indexing`
- Output node: `False`

The LLMTreeIndex node is designed for creating an index structure from a set of nodes using a specified language model. It facilitates the organization and retrieval of information by structuring it in a tree-like index, enhancing the efficiency of data navigation and access.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to be used for indexing. It plays a crucial role in determining how the nodes are analyzed and organized within the tree structure.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `str`
- **`llm_nodes`**
    - The set of nodes to be indexed. This parameter is essential for defining the scope and content of the tree index.
    - Comfy dtype: `LLM_NODES`
    - Python dtype: `List`
### Optional
- **`service_context`**
    - Provides additional context for the service, influencing how the indexing process is tailored to specific needs.
    - Comfy dtype: `LLM_CONTEXT`
    - Python dtype: `Optional[Dict]`
- **`num_children`**
    - Determines the maximum number of child nodes each node in the tree can have, affecting the breadth of the tree structure.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`build_tree`**
    - Controls whether the tree structure is actually constructed, allowing for flexibility in the indexing process.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`llm_index`**
    - Comfy dtype: `LLM_INDEX`
    - The resulting tree-like index structure, organized according to the specified language model and nodes.
    - Python dtype: `TreeIndex`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMTreeIndex:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
                "llm_nodes": ("LLM_NODES",),
            },
            "optional": {
                "service_context": ("LLM_CONTEXT",),
                "num_children": ("INT", {"default": 10}),
                "build_tree": ("BOOLEAN", {"default": True}),
            },
        }

    RETURN_TYPES = ("LLM_INDEX",)
    RETURN_NAMES = ("llm_index",)

    FUNCTION = "index"
    CATEGORY = "SALT/Llama-Index/Indexing"

    def index(self, llm_model, llm_nodes, service_context=None, num_children=10, build_tree=True):
        index = TreeIndex(
            nodes=llm_nodes,
            num_children=num_children,
            build_tree=build_tree,
            use_async=False,
            show_progress=True,
            service_context=service_context,
        )
        return (index,)

```
