# Gemini LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a text generation/completion request against Google Gemini models through Salt’s LiteLLM service. It supports dynamic prompt composition with injected context inputs and configurable generation controls (temperature and max tokens). The node fetches an available model list from the service (with caching) and falls back to a predefined set if the service is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgemini.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need Gemini-family LLM responses (analysis, summaries, drafting, Q&A). Provide a clear prompt and optionally pass up to four context strings to be merged into the prompt using placeholders like {{input_1}}. Select a Gemini model appropriate for your task and tune temperature and max_tokens to control creativity and response length. Chain this node after nodes that produce textual context, and connect its output to downstream logic or display nodes.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select which Gemini (or Gemma) model to use. The list is fetched from the LLM service; if unavailable, a fallback list is shown.</td><td style="word-wrap: break-word;">gemini-2.5-flash</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions that set behavior, tone, and formatting rules for the model.</td><td style="word-wrap: break-word;">Respond concisely and use bullet points when listing steps.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The main user message. You can reference optional inputs using placeholders like {{input_1}} ... {{input_4}} to inject context.</td><td style="word-wrap: break-word;">Summarize the following notes: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness/creativity. Lower is more deterministic; higher is more diverse.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum tokens in the response. Set higher for longer answers; 0 lets the backend/provider default apply.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to inject into the prompt via {{input_1}}.</td><td style="word-wrap: break-word;">Meeting notes from 2025-01-02</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to inject into the prompt via {{input_2}}.</td><td style="word-wrap: break-word;">Customer feedback excerpts</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to inject into the prompt via {{input_3}}.</td><td style="word-wrap: break-word;">Product specification</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to inject into the prompt via {{input_4}}.</td><td style="word-wrap: break-word;">Known issues list</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The LLM-generated text response.</td><td style="word-wrap: break-word;">Here is a concise summary of the meeting notes...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Provider**: This node targets the Gemini provider and queries models via the Salt LiteLLM service.
- **Dynamic prompt variables**: The node replaces placeholders {{input_1}}..{{input_4}} in the prompt with the corresponding optional input values.
- **Model list caching**: Available models are cached for about 1 hour. If the service cannot list models, a fallback set (e.g., gemini-2.5-flash, gemini-2.5-pro, gemma-3 variants) is shown.
- **Max tokens**: If the response stops early due to length, increase max_tokens.
- **Quotas**: This node counts toward LLM node usage limits in workflows.
- **Service configuration**: Your workspace must have Gemini access configured in the Salt LLM service (e.g., valid provider credentials) for requests to succeed.

## Troubleshooting
- **Model ID not found**: Select a model from the provided dropdown (don’t type a custom name) and retry.
- **Response truncated or cut off**: Increase max_tokens or reduce prompt/context length.
- **Slow or timeout errors**: Simplify the prompt, reduce context size, or try a faster model (e.g., a 'flash' variant). If persistent, contact your admin to verify Gemini provider availability and service health.
- **Empty or generic responses**: Lower temperature for more focused output, or improve the system_prompt to better constrain style and structure.
- **Placeholders not replaced**: Ensure you actually used {{input_1}}..{{input_4}} in the prompt and provided the corresponding optional inputs.
