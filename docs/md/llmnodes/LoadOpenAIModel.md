# Load OpenAI Model
## Documentation
- Class name: `LoadOpenAIModel`
- Category: `LLM Nodes`
- Output node: `False`

This node is designed to facilitate the integration of OpenAI's language models into the user's workflow by loading a specified model and its corresponding embedding model. It also allows for the adjustment of the model's generation behavior through temperature control. The output is a comprehensive object that includes both the language and embedding models, ready for use in various applications.
## Input types
- **`model`**
    - Specifies the OpenAI model to be loaded, offering a selection from a comprehensive list of available models. The default setting aims to provide a balance between performance and resource usage.
    - Python dtype: `str`
    - Comfy dtype: `STRING[COMBO]`
- **`temperature`**
    - Controls the randomness of the model's output, allowing for fine-tuning of the generation process. A lower temperature results in more deterministic outputs, while a higher value increases randomness.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`embed_model`**
    - Determines the embedding model to be used in conjunction with the language model, enhancing the model's understanding and processing of input data.
    - Python dtype: `str`
    - Comfy dtype: `STRING[COMBO]`
## Output types
- **`model`**
    - A structured object containing the loaded language model, embedding model, and a unique identifier, ready for integration into various applications.
    - Python dtype: `Dict[str, Any]`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown

The LoadOpenAIModel node is essential for integrating OpenAI's language models into the pipeline, allowing users to specify a model and its embedding counterpart for enhanced input understanding. It is often used with nodes that require advanced text processing and generation capabilities, providing both the language model and embedding model as output for diverse applications.
## Source code
```python
class LoadOpenAIModel:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "model": (sorted(ALL_AVAILABLE_MODELS.keys()), {"default": "gpt-4-turbo-preview"}),
                "temperature": ("FLOAT", {"default": 0.0, "min": 0.0, "max": 1.0}),
                "embed_model": (
                    sorted([x.value for x in OpenAIEmbeddingModelType]),
                    {"default": "text-embedding-3-small"},
                ),
            }
        }

    RETURN_TYPES = ("MODEL",)
    FUNCTION = "load_openai_model"
    CATEGORY = NAME

    def load_openai_model(self, model: str, temperature: int, embed_model: str) -> Dict[str, Any]:
        name = f"{model}_{embed_model}_{temperature}"
        llm = OpenAI(model=model, temperature=temperature)
        embed_model = OpenAIEmbedding(model=embed_model)
        return ({"llm": llm, "embed_model": embed_model, "name": name},)

```
