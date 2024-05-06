---
tags:
- LLM
- LLMChat
---

# ∞ Multi Query
## Documentation
- Class name: `LLMChat`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

The LLMChat node facilitates interactive chat sessions by leveraging a language model to generate responses based on user prompts and contextual information. It supports dynamic conversation flow, allowing for the integration of documents and messages to enrich the dialogue and provide more relevant and engaging responses.
## Input types
### Required
- **`llm_model`**
    - A dictionary containing the language model and potentially an embedding model, which are crucial for generating responses and processing the chat context.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`prompt`**
    - The user's input or question to the chatbot, serving as the basis for generating a response.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`llm_context`**
    - Optional context for the language model, enhancing its understanding and response accuracy.
    - Comfy dtype: `LLM_CONTEXT`
    - Python dtype: `Dict[str, Any]`
- **`llm_message`**
    - A list of chat messages that may influence the response by providing additional context or dialogue history.
    - Comfy dtype: `LIST`
    - Python dtype: `List[ChatMessage]`
- **`llm_documents`**
    - A list of documents that can be used to enrich the chatbot's responses by providing additional information or context.
    - Comfy dtype: `DOCUMENT`
    - Python dtype: `List[Document]`
## Output types
- **`response`**
    - Comfy dtype: `STRING`
    - The generated response from the chatbot, based on the user's prompt and the provided context.
    - Python dtype: `str`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChat:
    def __init__(self):
        pass

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL", ),
                "prompt": ("STRING", {"multiline": True, "dynamicPrompts": False}),
            },
            "optional": {
                  "llm_context": ("LLM_CONTEXT", ),
                  "llm_message": ("LIST", {}),
                  "llm_documents": ("DOCUMENT", {}),
            }
        }

    RETURN_TYPES = ("STRING", )
    RETURN_NAMES = ("response", )

    FUNCTION = "chat"
    CATEGORY = "SALT/Llama-Index/Querying"

    def chat(self, llm_model:Dict[str, Any], prompt:str, llm_context:Any = None, llm_message:List[ChatMessage] = None, llm_documents:List[Any] = None) -> str:
        response = llm_model['llm'].complete(prompt)

        # Spoof documents -- Why can't we just talk to a modeL?
        if not llm_documents:
            documents = [Document(text="null", extra_info={})]
        else:
            documents = llm_documents

        index = VectorStoreIndex.from_documents(
            documents, 
            service_context=llm_context,
            transformations=[SentenceSplitter(chunk_size=1024, chunk_overlap=20)]
        )
        
        if not llm_message:
            llm_message = [ChatMessage(MessageRole.USER, content="")]

        if not prompt.strip():
            prompt = "null"

        template = ChatPromptTemplate(message_templates=llm_message)

        query_engine = index.as_query_engine(llm=llm_model.get("llm", None), embed_model=llm_model.get("embed_model", None), text_qa_template=template)

        response = query_engine.query(prompt)

        pprint(response, indent=4)
        return (response.response, )

```
