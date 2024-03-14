# Load OpenAI Model
## Documentation
- Class name: `LoadOpenAIModel`
- Category: `LLM Nodes`
- Output node: `False`

This node is responsible for loading an OpenAI model along with its embedding model based on the specified parameters. It creates a unique identifier for the loaded model combination by concatenating the model names and temperature setting. This allows for the flexible use of different OpenAI models and their embeddings in downstream tasks.
## Input types
### Required
- **`model`**
    - Specifies the OpenAI model to be loaded. The choice of model can significantly impact the behavior and capabilities of the generated outputs. It is selected from a predefined list of available models, allowing for customization based on the task at hand.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
- **`temperature`**
    - Controls the randomness of the output generation. A higher temperature results in more random outputs, while a lower temperature produces more deterministic outputs.
    - Python dtype: `float`
    - Comfy dtype: `FLOAT`
- **`embed_model`**
    - Determines the embedding model to be used alongside the main OpenAI model. This affects how inputs are processed and represented internally. The embedding model is chosen from a predefined list, enabling tailored input processing.
    - Python dtype: `str`
    - Comfy dtype: `MODEL`
## Output types
- **`model`**
    - The combined OpenAI model and embedding model loaded based on the specified parameters.
    - Python dtype: `Dict[str, Any]`
    - Comfy dtype: `MODEL`
## Usage tips
- Infra type: `GPU`
- Common nodes: unknown

Often used with downstream tasks such as text generation, summarization, or embedding extraction, the LoadOpenAIModel node loads a specified OpenAI model and its embedding counterpart based on user-defined parameters, enabling tailored AI functionalities.
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
