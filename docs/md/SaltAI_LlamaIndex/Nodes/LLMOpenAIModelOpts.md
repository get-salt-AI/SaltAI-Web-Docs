---
tags:
- LLM
---

# ∞ OpenAI Model Options
## Documentation
- Class name: `LLMOpenAIModelOpts`
- Category: `SALT/Llama-Index/Loaders/Options`
- Output node: `False`

The LLMOpenAIModelOpts node is designed to configure and customize the options for an OpenAI model, allowing for adjustments to parameters such as temperature, token limits, and embedding settings. It serves as a flexible interface for fine-tuning the behavior of the OpenAI model to better suit specific requirements or preferences.
## Input types
### Required
- **`llm_model`**
    - Specifies the OpenAI model to be configured, including its initial settings and state.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
### Optional
- **`model_temperature`**
    - Sets the temperature for the model, influencing the randomness of the output. A lower temperature results in more deterministic outputs, while a higher temperature allows for more varied responses.
    - Comfy dtype: `FLOAT`
    - Python dtype: `float`
- **`model_max_tokens`**
    - Defines the maximum number of tokens the model can generate in a single response, limiting the length of the output.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`model_api_max_retries`**
    - Determines the maximum number of retries for API requests in case of failures, enhancing robustness.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`model_api_timeout`**
    - Specifies the timeout duration for API requests, ensuring timely responses.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`model_reuse_anyscale_client`**
    - Controls whether to reuse the Anyscale client across multiple requests, potentially improving performance.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`multimodal_max_new_tokens`**
    - For multimodal models, sets the maximum number of new tokens that can be generated, affecting the length of multimodal outputs.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`multimodal_image_detail`**
    - Adjusts the level of detail for images in multimodal responses, with options including low, high, and auto.
    - Comfy dtype: `COMBO[STRING]`
    - Python dtype: `str`
- **`embed_batch_size`**
    - Configures the batch size for embedding operations, impacting performance and resource usage.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`embed_dimensions`**
    - Sets the dimensionality of the embeddings, influencing the detail and quality of the generated embeddings.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`embed_api_max_retries`**
    - Specifies the maximum number of retries for embedding API requests, enhancing reliability.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`embed_api_timeout`**
    - Determines the timeout for embedding API requests, ensuring efficient processing.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`embed_reuse_anyscale_client`**
    - Indicates whether to reuse the Anyscale client for embedding operations, potentially improving efficiency.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`model_additional_kwargs`**
    - Allows for the specification of additional, custom keyword arguments for the model, offering extended configurability.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`embed_additional_kwargs`**
    - Enables the provision of extra, custom keyword arguments for embedding operations, allowing for further customization.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`model_system_prompt`**
    - unknown
    - Comfy dtype: `STRING`
    - Python dtype: `unknown`
## Output types
- **`model`**
    - Comfy dtype: `LLM_MODEL`
    - Returns the configured OpenAI model with the specified options applied, ready for use in subsequent operations.
    - Python dtype: `Dict[str, Any]`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMOpenAIModelOpts:
    """
    Sets various options for the model, and embedding.
    """
    def __init__(self):
        pass
    
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
            },
            "optional": {
                "model_temperature": ("FLOAT", {"min": 0, "max": 1.0, "default": DEFAULT_TEMPERATURE, "step": 0.001}),
                "model_max_tokens": ("INT", {"min": 8, "default": 4096}),
                "model_api_max_retries": ("INT", {"min": 1, "max": 12, "default": 3}),
                "model_api_timeout": ("INT", {"min": 8, "max": 120, "default": 60}),
                "model_reuse_anyscale_client": ("BOOLEAN", {"default": True}),
                
                "multimodal_max_new_tokens": ("INT", {"min": 8, "default": 300}),
                "multimodal_image_detail": (["low", "high", "auto"],),

                "embed_batch_size": ("INT", {"min": 8, "default": DEFAULT_EMBED_BATCH_SIZE}),
                "embed_dimensions": ("INT", {"min": 1, "default": DEFAULT_EMBEDDING_DIM}),
                "embed_api_max_retries": ("INT", {"min": 1, "max": 12, "default": 3}),
                "embed_api_timeout": ("INT", {"min": 8, "max": 120, "default": 60}),
                "embed_reuse_anyscale_client": ("BOOLEAN", {"default": True}),
                
                "model_additional_kwargs": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "", "placeholder": "Additional model kwargs JSON"}),
                "embed_additional_kwargs": ("STRING", {"multiline": True, "dynamicPrompts": False, "default": "", "placeholder": "Additional embed kwargs JSON"}),
                "model_system_prompt": ("STRING", {"multiline": True, "dynamicPrompts": False, "placeholder": "System directions, or rules to follow globally across nodes."}),
            }
        }

    RETURN_TYPES = ("LLM_MODEL", )
    RETURN_NAMES = ("model", )

    FUNCTION = "set_options"
    CATEGORY = "SALT/Llama-Index/Loaders/Options"

    def set_options(self, llm_model:Dict[str, Any], **kwargs) -> Dict[str, Any]:
        llm = llm_model['llm']
        embed = llm_model['embed_model']
        
        # LLM Options
        llm.temperature = kwargs.get("model_temperature", DEFAULT_TEMPERATURE)
        llm.max_retries = kwargs.get("model_api_max_retries", 3)
        llm.reuse_client = kwargs.get("model_reuse_anyscale_client", True)
        llm.additional_kwargs = json.loads(kwargs.get("model_additional_kwargs", {}).strip()) if kwargs.get("model_additional_kwargs", {}).strip() != "" else {} # Default to `None` if empty string
        llm.system_prompt = kwargs.get("model_system_prompt", None)

        # Embed Options
        embed.embed_batch_size = kwargs.get("embed_batch_size", DEFAULT_EMBED_BATCH_SIZE)
        embed.dimensions = kwargs.get("embed_dimensions", DEFAULT_EMBEDDING_DIM) if kwargs.get("embed_dimensions", DEFAULT_EMBEDDING_DIM) > 0 else None # Default to `None` if not above 0
        embed.additional_kwargs = json.loads(kwargs.get("embed_additional_kwargs", {}).strip()) if kwargs.get("embed_additional_kwargs", "").strip() != "" else {} # Default to `None` if empty string
        embed.max_retries = kwargs.get("embed_api_max_retries", 3)
        embed.timeout = kwargs.get("embed_api_timeout", 60)
        embed.reuse_client = kwargs.get("embed_reuse_anyscale_client", True)

        if isinstance(llm, OpenAIMultiModal):
            llm.max_new_tokens = kwargs.get("multimodal_max_new_tokens", 300)
            llm.image_detail = kwargs.get("multimodal_image_detail", "low")
        else:
            llm.max_tokens = kwargs.get("model_max_tokens", 4096)

        llm_model['llm'] = llm
        llm_model['embed_model'] = embed

        return (llm_model,)

```
