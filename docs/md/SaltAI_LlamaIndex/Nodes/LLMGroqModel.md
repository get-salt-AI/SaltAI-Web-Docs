---
tags:
- LLM
---

# ∞ Groq Model
## Documentation
- Class name: `LLMGroqModel`
- Category: `SALT/Llama-Index/Loaders`
- Output node: `False`

The LLMGroqModel node is designed to facilitate the integration and utilization of language models with Groq's API, enabling the loading and embedding of models for advanced natural language processing tasks. It abstracts the complexities involved in interfacing with Groq's API and OpenAI's embedding models, providing a streamlined way to access and leverage these powerful tools for language-related applications.
## Input types
### Required
- **`model`**
    - Specifies the language model to be loaded. This parameter is crucial as it determines the specific language model that will be utilized for processing and embedding tasks.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`groq_api_key`**
    - The API key required to authenticate and interact with Groq's API. This key is essential for accessing Groq's services and performing operations with the specified language model.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`embedding_model`**
    - Defines the OpenAI embedding model to be used. This parameter is important for specifying the embedding model that will complement the primary language model in processing tasks.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
### Optional
- **`openai_api_key`**
    - Optional API key for OpenAI, used when interacting with OpenAI's services for embedding purposes. It enhances the node's functionality by allowing access to OpenAI's embedding models.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`llm_model`**
    - Comfy dtype: `LLM_MODEL`
    - Returns the loaded language model along with its name, facilitating further operations and processing.
    - Python dtype: `Dict[str, Any]`
- **`embed_model_only`**
    - Comfy dtype: `LLM_EMBED_MODEL`
    - Provides access to the OpenAI embedding model and its name, enabling specialized embedding tasks separate from the primary language model.
    - Python dtype: `Dict[str, Any]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMGroq:
    """
    @Documentation: https://docs.llamaindex.ai/en/stable/api_reference/llms/groq/
    """
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "model": (["llama3-8b-8192", "llama3-70b-8192", "mixtral-8x7b-32768", "gemma-7b-it"],),
                "groq_api_key": ("STRING", {
                    "multiline": False,
                    "dynamicPrompts": False,
                    "default": os.environ.get("GROQ_API_KEY", "")
                }),
                "embedding_model": (
                    sorted([x.value for x in OpenAIEmbeddingModelType]),
                    {"default": "text-embedding-ada-002"},
                ),
            },
            "optional": {
                "openai_api_key": ("STRING", {
                    "multiline": False,
                    "dynamicPrompts": False,
                    "default": os.environ.get("OPENAI_API_KEY", "")
                }),
            }
        }

    RETURN_TYPES = ("LLM_MODEL", "LLM_EMBED_MODEL")
    RETURN_NAMES = ("llm_model", "embed_model_only")

    FUNCTION = "load_model"
    CATEGORY = "SALT/Llama-Index/Loaders"

    def load_model(self, model: str, groq_api_key: str, embedding_model:str, openai_api_key:str = None) -> Dict[str, Any]:
        llm = Groq(model=model, api_key=groq_api_key)
        embed_model = OpenAIEmbedding(model_name=embedding_model, api_key=openai_api_key,)
        return ({"llm": llm, "llm_name": model, "embed_model": embed_model, "embed_name": embedding_model}, {"embed_model": embed_model, "embed_name": embedding_model})

```
