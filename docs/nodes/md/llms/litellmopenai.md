# OpenAI LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends a chat-style prompt to an OpenAI model via the LiteLLM service and returns the model's text response. It supports a system prompt, dynamic placeholders in the user prompt, temperature control, and an optional max_tokens limit. The node auto-populates available OpenAI models (with 1-hour caching) and falls back to a built-in list if the service is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmopenai.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to perform text generation, reasoning, summarization, or transformation with OpenAI models. Select a model (e.g., gpt-4o), set a system prompt for behavior and style, and provide a user prompt. You can connect additional text inputs (input_1–input_4) to knowledge sources or context and reference them in the prompt using placeholders like {{input_1}}. Adjust temperature for creativity and max_tokens for response length.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">OpenAI model to use. Populated from the LLM service; falls back to a curated list if fetching fails.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">System instructions that set the assistant's behavior, style, and constraints.</td><td style="word-wrap: break-word;">Respond concisely in bullet points and include actionable next steps.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">User prompt. Supports dynamic placeholders for inputs: {{input_1}}, {{input_2}}, {{input_3}}, {{input_4}}.</td><td style="word-wrap: break-word;">Summarize the following document: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls creativity and variability. Lower values yield more deterministic responses.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Upper limit for the number of tokens in the response. Set to 0 to use provider defaults.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input for additional context (referenced in prompt via {{input_1}}).</td><td style="word-wrap: break-word;">Quarterly financial report text</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input for additional context (referenced via {{input_2}}).</td><td style="word-wrap: break-word;">Customer feedback notes</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input for additional context (referenced via {{input_3}}).</td><td style="word-wrap: break-word;">Product requirements</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input for additional context (referenced via {{input_4}}).</td><td style="word-wrap: break-word;">Support ticket summaries</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text response from the selected OpenAI model.</td><td style="word-wrap: break-word;">Here are the key insights from the document...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model sourcing**: The model dropdown is fetched from the LLM service and cached for ~1 hour. If fetching fails, a fallback list of OpenAI models is shown.
- **Dynamic placeholders**: You can embed {{input_1}}–{{input_4}} in the prompt. These will be replaced with the corresponding inputs at runtime.
- **Response truncation**: If the response hits the max_tokens limit, it may be truncated (finish_reason: length). Increase max_tokens to allow longer output.
- **Timeouts**: Requests have a default timeout of about 180 seconds. Long-running requests are kept alive internally with heartbeats.
- **Additional parameters**: Temperature and max_tokens are passed to the underlying service; other provider-specific parameters may be supported when added by extended nodes.

## Troubleshooting
- **Model ID not found**: Ensure the selected model exists in the dropdown. If you typed a custom value, select a listed model or wait for the list to refresh.
- **Truncated output**: If the response seems cut off, increase max_tokens and try again.
- **No placeholder substitution**: Verify your prompt uses the exact placeholder names (e.g., {{input_1}}) and that the corresponding input is connected or provided.
- **Request timeout**: For large contexts or complex models, increase the timeout (if exposed by your environment) or simplify the prompt/context.
- **Empty or low-quality output**: Reduce temperature for more focused responses, or provide clearer instructions and examples in the system/user prompts.
