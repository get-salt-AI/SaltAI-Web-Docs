---
tags:
- LLM
- LLMChat
---

# ∞ Chat Engine
## Documentation
- Class name: `LLMChatBot`
- Category: `SALT/Llama-Index/Querying`
- Output node: `False`

The LLMChatBot node facilitates interactive chat sessions between a user and an AI assistant, leveraging a specified language model. It supports dynamic conversation flow, allowing for the customization of user and system nicknames, and the option to reset the conversation engine.
## Input types
### Required
- **`llm_model`**
    - Specifies the language model to be used for the chat session, including any necessary configuration details.
    - Comfy dtype: `LLM_MODEL`
    - Python dtype: `Dict[str, Any]`
- **`llm_context`**
    - Provides context or state information for the language model, which can influence the generation of responses. This context can include previous conversation history or specific settings tailored to the chat session.
    - Comfy dtype: `LLM_CONTEXT`
    - Python dtype: `Dict[str, Any]`
- **`prompt`**
    - The user's input message to which the AI assistant will generate a response.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
### Optional
- **`reset_engine`**
    - A flag to reset the conversation engine, useful for starting a new conversation thread.
    - Comfy dtype: `BOOLEAN`
    - Python dtype: `bool`
- **`user_nickname`**
    - Custom nickname for the user in the chat session.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
- **`system_nickname`**
    - Custom nickname for the AI assistant in the chat session.
    - Comfy dtype: `STRING`
    - Python dtype: `str`
## Output types
- **`chat_history`**
    - Comfy dtype: `STRING`
    - A record of the chat session, including both user and AI assistant messages.
    - Python dtype: `str`
- **`response`**
    - Comfy dtype: `STRING`
    - The generated response from the AI assistant to the user's input prompt.
    - Python dtype: `str`
- **`chat_token_count`**
    - Comfy dtype: `INT`
    - The total number of tokens generated during the chat session.
    - Python dtype: `int`
## Usage tips
- Infra type: `CPU`
- Common nodes: unknown


## Source code
```python
class LLMChatBot:
    def __init__(self):
        self.chat_history = []
        self.history = []
        self.token_map = {} 

    @classmethod
    def INPUT_TYPES(cls):
        return {
            "required": {
                "llm_model": ("LLM_MODEL", ),
                "llm_context": ("LLM_CONTEXT", ),
                "prompt": ("STRING", {"multiline": True, "dynamicPrompt": False}),
            },
            "optional": {
                "reset_engine": ("BOOLEAN", {"default": False}),
                "user_nickname": ("STRING", {"default": "User"}),
                "system_nickname": ("STRING", {"default": "Assistant"})
            }
        }

    RETURN_TYPES = ("STRING", "STRING", "INT")
    RETURN_NAMES = ("chat_history", "response", "chat_token_count")

    FUNCTION = "chat"
    CATEGORY = "SALT/Llama-Index/Querying"

    def chat(self, llm_model: Dict[str, Any], llm_context:Any, prompt: str, reset_engine:bool = False, user_nickname:str = "User", system_nickname:str = "Assistant") -> str:

        if reset_engine:
            self.chat_history.clear()
            self.history.clear()
            self.token_map.clear()

        tokenizer = tiktoken.encoding_for_model(llm_model.get('llm_name', 'gpt-3-turbo'))
        max_tokens = llm_model.get("max_tokens", 4096)

        if not self.chat_history:
            system_prompt = getattr(llm_model['llm'], "system_prompt", None)
            if system_prompt not in (None, ""):
                initial_msg = ChatMessage(role=MessageRole.SYSTEM, content=system_prompt)
                self.chat_history.append(initial_msg)
                self.token_map[0] = tokenizer.encode(system_prompt)

        # Tokenize and count initial tokens
        cumulative_token_count = 0
        for index, message in enumerate(self.chat_history):
            if index not in self.token_map:
                self.token_map[index] = tokenizer.encode(message.content)
            cumulative_token_count += len(self.token_map[index])

        # Prune messages from the history if over max_tokens
        index = 0
        while cumulative_token_count > max_tokens and index < len(self.chat_history):
            tokens = self.token_map[index]
            if len(tokens) > 1:
                tokens.pop(0)
                self.chat_history[index].content = tokenizer.decode(tokens)
                cumulative_token_count -= 1
            else:
                cumulative_token_count -= len(tokens)
                self.chat_history.pop(index)
                self.token_map.pop(index)
                for old_index in list(self.token_map.keys()):
                    if old_index > index:
                        self.token_map[old_index - 1] = self.token_map.pop(old_index)
                continue
            index += 1
                
        history_string = ""
        reply_string = ""
        documents = []

        # Build prior history string
        for history in self.history:
            user, assistant, timestamp = history
            history_string += f"""[{user_nickname}]: {history[user]}

[{system_nickname}]: {history[assistant]}

"""
        # Spoof documents -- Why can't we just talk to a modeL?
        documents = [Document(text="null", extra_info={})]

        index = VectorStoreIndex.from_documents(
            documents, 
            service_context=llm_context,
            transformations=[SentenceSplitter(chunk_size=1024, chunk_overlap=20)]
        )
        chat_engine = index.as_chat_engine(chat_mode="best")

        response = chat_engine.chat(prompt, chat_history=self.chat_history)

        response_dict = {
            user_nickname: prompt, 
            system_nickname: response.response,
            "timestamp": str(time.time())
        }

        user_cm = ChatMessage(role=MessageRole.USER, content=prompt)
        system_cm = ChatMessage(role=MessageRole.SYSTEM, content=response.response)
        self.chat_history.append(user_cm)
        self.chat_history.append(system_cm)

        self.history.append(response_dict)

        reply_string = response.response

        history_string += f"""[{user_nickname}]: {prompt}

[{system_nickname}]: {response.response}"""
            
        pprint(self.chat_history, indent=4)

        return (history_string, reply_string, cumulative_token_count)

```
