---
tags:
- LLM
---

# ∞ OpenAI Model
## Documentation
- Class name: `LLMOpenAIModel`
- Category: `SALT/Llama-Index/Loaders`
- Output node: `False`

The LLMOpenAIModel node is designed to load and initialize models from OpenAI, including support for multimodal models. It encapsulates the functionality to authenticate with OpenAI's API, select specific models for language tasks or embeddings, and configure them for use in various applications.
## Input types
### Required
- **`model`**
    - Specifies the OpenAI model to be loaded. This selection determines the capabilities and performance of the loaded language model.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`api_key`**
    - The API key for authenticating with OpenAI's service. It is essential for accessing the models and their functionalities.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`embedding_model`**
    - Defines the embedding model to be used alongside the main language model. This choice affects the type of embeddings that can be generated, tailored to specific tasks.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
### Optional
- **`multimodal`**
    - A boolean flag indicating whether to load a multimodal model, enabling the processing of both text and non-text inputs.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
## Output types
- **`llm_model`**
    - Comfy dtype: `LLM_MODEL`
    - The loaded language model, ready for performing language tasks.
    - Python dtype: `Dict[str, Any]`
- **`embed_model_only`**
    - Comfy dtype: `LLM_EMBED_MODEL`
    - The embedding model loaded separately, focused on generating embeddings.
    - Python dtype: `Dict[str, Any]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMOpenAI:
    """
    @Documentation: https://docs.llamaindex.ai/en/stable/api_reference/llms/openai/
    """
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "model": (list(ALL_AVAILABLE_MODELS),),
                "api_key": ("STRING", {
                    "multiline": False, 
                    "dynamicPrompts": False, 
                    "default": os.environ.get("OPENAI_API_KEY", "")
                }),
                "embedding_model": (
                    sorted([x.value for x in OpenAIEmbeddingModelType]),
                    {"default": "text-embedding-ada-002"},
                ),
            },
            "optional": {
                "multimodal": ("BOOLEAN", {"default": False})
            }
        }

    RETURN_TYPES = ("LLM_MODEL", "LLM_EMBED_MODEL")
    RETURN_NAMES = ("llm_model", "embed_model_only")

    FUNCTION = "load_model"
    CATEGORY = "SALT/Llama-Index/Loaders"

    def load_model(self, model:str, embedding_model:str, api_key:str, multimodal:bool = False) -> Dict[str, Any]:
        llm = OpenAI(model=model) if not multimodal else OpenAIMultiModal(model=model)
        llm.api_key = api_key
        embed_model = OpenAIEmbedding(model_name=embedding_model, api_key=api_key,)
        return ({"llm":llm, "llm_name": model, "embed_model":embed_model, "embed_name": embedding_model}, {"embed_model": embed_model, "embed_name": embedding_model})

```
