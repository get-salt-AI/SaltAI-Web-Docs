---
tags:
- LLM
---

# ∞ LlamaCPP Model
## Documentation
- Class name: `LLMLlamaCPPModel`
- Category: `SALT/Llama-Index/Loaders`
- Output node: `False`

The LLMLlamaCPPModel node is designed to load and initialize LlamaCPP models, along with embedding models, for use in various machine learning and natural language processing tasks. It abstracts the complexities of setting up these models, providing a streamlined interface for integrating advanced AI capabilities.
## Input types
### Required
- **`ModelName`**
    - The 'ModelName' parameter specifies the name of the LlamaCPP model to be loaded. This name is used to locate the model file within a predefined directory structure, ensuring the correct model is initialized for the task.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
## Output types
- **`model`**
    - Comfy dtype: `LLM_MODEL`
    - This output represents the loaded LlamaCPP model along with its associated embedding model, encapsulating both the model's computational graph and its pre-trained weights for immediate use in applications.
    - Python dtype: `Dict[str, Union[LlamaCPP, HuggingFaceEmbedding]]`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown


## Source code
```python
class LLMLlamaCPP:
    """
    @Documentation: https://docs.llamaindex.ai/en/stable/api_reference/llms/llama_cpp/
    """
    def __init__(self):
        pass
    
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "ModelName": (LOCAL_FILES, ), 
            },
        }

    RETURN_TYPES = ("LLM_MODEL", )
    RETURN_NAMES = ("model", )

    FUNCTION = "load_model"
    CATEGORY = "SALT/Llama-Index/Loaders"

    def load_model(self, ModelName: str) -> Dict[str, Any]:
        path = folder_paths.get_full_path('llm',ModelName)
        llm = LlamaCPP(model_path=path)
        embed_model = HuggingFaceEmbedding(model_name="BAAI/bge-small-en-v1.5")
        return ({"llm":llm, "llm_name": ModelName,"embed_model": embed_model, "embed_name": "BAAI/bge-small-en-v1.5"},)

```
