# Groq LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a text generation request against Groq-hosted large language models via the LiteLLM service layer. It supports dynamic prompt composition, forwards common provider parameters (like temperature and max_tokens), and automatically lists available Groq models (with a safe fallback list if discovery fails).

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgroq.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a Groq-backed LLM to generate or transform text. Typical flow: select a Groq model, provide a system prompt to set behavior, craft a user prompt (optionally referencing {{input_1}}–{{input_4}}), and tune temperature/max_tokens. Connect external context (e.g., knowledge base outputs) into input_1..input_4 to enrich the prompt before sending to the LLM. The node outputs the model’s text response as a single string.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The Groq model to use. The list is fetched from the LLM service and falls back to a curated Groq model list if the service is unavailable.</td><td style="word-wrap: break-word;">llama-3.1-8b-instant</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Instructions that set behavior, tone, and formatting for the assistant. If non-empty, it is sent as a system message.</td><td style="word-wrap: break-word;">Respond concisely and use bullet points when appropriate.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The main user message. You can reference optional inputs using double-brace placeholders like {{input_1}}–{{input_4}} to inject external context.</td><td style="word-wrap: break-word;">Summarize the following context: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness/creativity (0=deterministic, 1=more diverse). Passed to the provider.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Upper bound on the length of the generated response. 0 lets the provider decide.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context to merge into the prompt via {{input_1}}.</td><td style="word-wrap: break-word;">Company knowledge base excerpt A</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context to merge into the prompt via {{input_2}}.</td><td style="word-wrap: break-word;">Company knowledge base excerpt B</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context to merge into the prompt via {{input_3}}.</td><td style="word-wrap: break-word;">User profile details</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context to merge into the prompt via {{input_4}}.</td><td style="word-wrap: break-word;">Latest ticket transcript</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The LLM’s generated text response.</td><td style="word-wrap: break-word;">Here is a concise summary of your context: ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model list discovery is cached per provider for about 1 hour; newly added models may not appear until the cache expires.
- If the model catalog service is unavailable, the node uses a built-in Groq fallback list (e.g., llama and compatible models).
- You can inject external context into the prompt using {{input_1}}–{{input_4}} placeholders; ensure the placeholders match the connected inputs.
- temperature and max_tokens (and any other advanced parameters you add) are forwarded to the LLM service as provider parameters.
- A default timeout of approximately 180 seconds applies; very long or slow generations may require adjusting the timeout via advanced parameters.

## Troubleshooting
- Model ID not found: Select a model from the offered list or wait for model cache refresh. If using a newly released model, try again after the cache window.
- Truncated response or incomplete answer: Increase max_tokens; a finish reason of 'length' indicates the limit was hit.
- Slow or timed-out requests: Reduce prompt size/complexity, pick a smaller model, or increase the timeout via advanced parameters.
- Placeholders not replaced: Verify you used double braces (e.g., {{input_1}}) and that the corresponding inputs are connected or have values.
- Request failed errors: Ensure the LLM service is reachable and provider credentials/configuration for Groq are correctly set in your environment.
