---
tags:
- LLM
---

# ∞ Service Context (Advanced)
## Documentation
- Class name: `LLMServiceContextAdv`
- Category: `SALT/Llama-Index/Context`
- Output node: `False`

This node is designed to create an advanced service context for language model operations, incorporating various configurations such as chunk size, overlap, context window, and output number to tailor the processing environment for specific needs. It enables fine-tuning of the language model's interaction parameters, enhancing the adaptability and efficiency of language processing tasks.
## Input types
### Required
- **`llm_model`**
    - The language model and embedding model configuration, essential for defining the operational context of the service.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
### Optional
- **`llm_embed_model`**
    - Specifies the embedding model to use, allowing for customization beyond the default setting.
    - Comfy dtype: `LLM_EMBED`
    - Python dtype: `str`
- **`llm_node_parser`**
    - An optional parser for processing nodes, providing additional flexibility in handling language model outputs.
    - Comfy dtype: `LLM_NODE_PARSER`
    - Python dtype: `Optional[Any]`
- **`enable_chunk_overlap`**
    - A flag to enable or disable chunk overlap, influencing how consecutive chunks relate to each other.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`chunk_overlap`**
    - The amount of overlap between chunks, in terms of characters, when overlap is enabled.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`enable_context_window`**
    - A flag to enable or disable the context window setting, affecting the scope of context considered for each processing step.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`context_window`**
    - The size of the context window, in terms of characters, defining the breadth of context for language model operations.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`enable_num_output`**
    - A flag to enable or disable the setting for the number of outputs, affecting the volume of generated content.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`num_output`**
    - The number of outputs to generate, providing control over the quantity of language model responses.
    - Comfy dtype: `INT`
    - Python dtype: `int`
- **`enable_chunk_size_limit`**
    - A flag to enable or disable the chunk size limit, offering a mechanism to cap the chunk size for processing.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`chunk_size_limit`**
    - The maximum allowable size for a chunk, ensuring that processing does not exceed specified resource constraints.
    - Comfy dtype: `INT`
    - Python dtype: `int`
## Output types
- **`llm_context`**
    - Comfy dtype: `LLM_CONTEXT`
    - The configured service context, ready for use in language model operations.
    - Python dtype: `ServiceContext`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMServiceContextAdv:
    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL",),
            },
            "optional": {
                "llm_embed_model": ("LLM_EMBED",),
                "llm_node_parser": ("LLM_NODE_PARSER",),
                "enable_chunk_overlap": ("BOOLEAN", {"default": True}),
                "chunk_overlap": ("INT", {"default": 50, "min": 0, "max": 100}),
                "enable_context_window": ("BOOLEAN", {"default": True}),
                "context_window": ("INT", {"default": 4096, "min": 2048, "max": 8192}),
                "enable_num_output": ("BOOLEAN", {"default": True}),
                "num_output": ("INT", {"default": 256, "min": 64, "max": 1024}),
                "enable_chunk_size_limit": ("BOOLEAN", {"default": True}),
                "chunk_size_limit": ("INT", {"default": 1024, "min": 512, "max": 2048}),
            },
        }

    RETURN_TYPES = ("LLM_CONTEXT",)
    RETURN_NAMES = ("llm_context",)

    FUNCTION = "context"
    CATEGORY = "SALT/Llama-Index/Context"

    def context(self, 
        llm_model:Dict[str, Any], 
        llm_embed_model="default", 
        llm_node_parser=None, 
        enable_chunk_size=True, 
        chunk_size=1024, 
        enable_chunk_overlap=True,
        chunk_overlap=50, 
        enable_context_window=True, 
        context_window=4096, 
        enable_num_output=True,
        num_output=256, 
        enable_chunk_size_limit=True,
        chunk_size_limit=1024
    ):
        prompt_helper = None
        if enable_context_window and enable_num_output:
            prompt_helper = PromptHelper(
                context_window=context_window if enable_context_window else None,
                num_output=num_output if enable_num_output else None,
                chunk_overlap_ratio=(chunk_overlap / 100.0) if enable_chunk_overlap else None,
                chunk_size_limit=chunk_size_limit if enable_chunk_size_limit else None,
            )

        service_context = ServiceContext.from_defaults(
                llm=llm_model['llm'],
                prompt_helper=prompt_helper,
                embed_model=llm_embed_model if llm_embed_model != "default" else None,
                node_parser=llm_node_parser,
                chunk_size=chunk_size if enable_chunk_size else None,
                chunk_overlap=chunk_overlap if enable_chunk_overlap else None,
        )
        return (service_context,)

```
