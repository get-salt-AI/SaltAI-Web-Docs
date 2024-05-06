---
tags:
- LLM
---

# ∞ Service Context
## Documentation
- Class name: `LLMServiceContextDefault`
- Category: `SALT/Llama-Index/Context`
- Output node: `False`

This node is designed to create a default service context for language model operations, encapsulating essential configurations and models for language processing tasks.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model and embedding model to be used, setting the foundation for the service context.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
## Output types
- **`llm_context`**
    - Comfy dtype: `LLM_CONTEXT`
    - Provides the constructed service context, ready for use in language model operations.
    - Python dtype: `ServiceContext`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMServiceContextDefault:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
            },
        }

    RETURN_TYPES = ("LLM_CONTEXT",)
    RETURN_NAMES = ("llm_context",)

    FUNCTION = "context"
    CATEGORY = "SALT/Llama-Index/Context"

    def context(self, llm_model: Dict[str, Any]):
        service_context = ServiceContext.from_defaults(
            llm=llm_model['llm'],
            embed_model=llm_model['embed_model'],
        )
        return (service_context,)

```
