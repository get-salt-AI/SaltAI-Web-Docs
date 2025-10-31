# OpenAI LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends a prompt to an OpenAI-compatible large language model via Salt’s LLM service and returns the generated text. Supports a system prompt for instruction, a user prompt with dynamic placeholders, temperature, and max token controls. Automatically lists available OpenAI models from the service and falls back to a built-in set if the service is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmopenai.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate text, analyze content, or perform instruction-following tasks with OpenAI models. Provide a clear prompt, optionally combine it with context inputs (input_1–input_4) using {{placeholders}} in the prompt, select a model, and tune temperature and max_tokens to control creativity and length. Typically used in workflows for drafting, summarization, Q&A, content transformation, and reasoning steps.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Choose an OpenAI model to run. The list is fetched from the LLM service; if unavailable, a fallback list (e.g., gpt-4o, gpt-4o-mini, gpt-4, gpt-3.5-turbo, o-series) is shown.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Instructions that guide how the AI should respond (style, format, tone). Leave concise rules for best results.</td><td style="word-wrap: break-word;">You are a helpful, concise assistant. Use bullet points when appropriate.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">User message to the model. You can include placeholders like {{input_1}}–{{input_4}} to inject context dynamically.</td><td style="word-wrap: break-word;">Summarize the following document in 5 bullet points: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls creativity and randomness. Lower for deterministic outputs, higher for more varied responses.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum tokens in the response. Higher allows longer outputs but uses more compute.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input that can be referenced in the prompt as {{input_1}}.</td><td style="word-wrap: break-word;">Full text of a report to summarize</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input that can be referenced in the prompt as {{input_2}}.</td><td style="word-wrap: break-word;">User profile JSON for personalization</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input that can be referenced in the prompt as {{input_3}}.</td><td style="word-wrap: break-word;">Glossary of terms</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input that can be referenced in the prompt as {{input_4}}.</td><td style="word-wrap: break-word;">Style guide or formatting rules</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text response from the selected OpenAI model.</td><td style="word-wrap: break-word;">Here are five concise bullet points summarizing your document: ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node targets the OpenAI provider via the Salt LLM service and automatically retrieves the current model list; if retrieval fails, it uses a predefined fallback set.
- Dynamic placeholders {{input_1}}–{{input_4}} in the prompt are replaced with the corresponding input values before sending to the model.
- Default timeout is 90 seconds. Very long or complex requests may require prompt simplification or reduced max_tokens to avoid timeouts.
- If you select a model not present in the available options, the node will raise an error. Refresh model options or select a listed model.
- Temperature range is 0–1. Max tokens typically supports up to 4096 for responses, but actual limits depend on the selected model.

## Troubleshooting
- Model not found: Ensure you picked a model from the provided selector. If the list looks outdated, try refreshing the workflow or reloading the model list.
- Empty or truncated output: Increase max_tokens or reduce prompt/context length. Some models may have stricter token limits.
- Inconsistent answers: Lower temperature for more deterministic outputs (e.g., 0.1–0.3).
- Placeholders not replaced: Verify your prompt uses {{input_1}}–{{input_4}} exactly and that the respective inputs are connected/provided.
- Timeouts or service errors: Simplify the prompt, lower max_tokens, or try again. If persistent, check LLM service availability or credentials.
