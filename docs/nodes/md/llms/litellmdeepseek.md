# DeepSeek LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a text generation request against DeepSeek models through Salt’s LiteLLM connector. Supports dynamic prompts with contextual inputs, temperature control, and token limits. Automatically populates available models from the LLM service and falls back to a curated list if discovery is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmdeepseek.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to generate or transform text with DeepSeek (e.g., general chat, coding assistance, or reasoning). Connect optional context inputs from upstream nodes or knowledge sources, reference them inside the prompt with {{input_1}}–{{input_4}}, pick a model, and adjust temperature and max_tokens as needed. The node returns a single string containing the model’s reply.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">DeepSeek model to use. The list is fetched from the LLM service; if unavailable, a fallback list is provided.</td><td style="word-wrap: break-word;">deepseek-chat</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instruction that sets behavior, tone, and style for the model. Leave empty to omit a system message.</td><td style="word-wrap: break-word;">You are a concise assistant. Prefer bullet points and short sentences.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The main user prompt. You can reference optional inputs using {{input_1}}–{{input_4}} to inject external context.</td><td style="word-wrap: break-word;">Summarize the following context in 5 bullet points: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness and creativity (0 = deterministic, 1 = most creative).</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens in the response. Set to 0 to use the provider default.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Refer to it in the prompt as {{input_1}}.</td><td style="word-wrap: break-word;">Long document text or retrieved notes</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Refer to it in the prompt as {{input_2}}.</td><td style="word-wrap: break-word;">User profile or preferences</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Refer to it in the prompt as {{input_3}}.</td><td style="word-wrap: break-word;">Product specs or API output</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Refer to it in the prompt as {{input_4}}.</td><td style="word-wrap: break-word;">Previous conversation turns or extracted insights</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The DeepSeek model’s response text.</td><td style="word-wrap: break-word;">Here are five concise bullet points summarizing the provided context...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model options**: The node queries the platform’s LLM service for available DeepSeek models; if that fails, it uses a fallback list (e.g., deepseek-chat, deepseek-coder, deepseek-reasoner).
- **Dynamic variables**: Use {{input_1}}–{{input_4}} inside the prompt to inject optional context inputs.
- **Timeouts**: Requests default to a 180s timeout. Long-running requests are kept alive via internal heartbeats.
- **Token limits**: If the response is truncated due to max_tokens, you may see a length-related finish reason; increase max_tokens to allow longer outputs.
- **Caching**: The discovered model list is cached per provider for about 1 hour to reduce repeated lookups.
- **Provider**: This node targets the DeepSeek provider via the LiteLLM integration.

## Troubleshooting
- **Model ID not found**: Ensure you selected a model from the list or that the list has loaded. If discovery fails, try a known fallback like "deepseek-chat".
- **Truncated output (finish_reason: length)**: Increase max_tokens to allow a longer response.
- **Slow or timed-out requests**: Reduce prompt size/context, choose a lighter model, or increase the timeout if supported by your environment.
- **Unexpected or generic replies**: Refine the system_prompt to set clearer behavior, lower temperature for precision, and provide structured context via inputs.
- **Variables not replaced in prompt**: Verify you used the correct placeholders (e.g., {{input_1}}) and that the corresponding inputs are connected or non-empty.
