# Gemini LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a prompt against Google’s Gemini models via the LiteLLM service. Builds a chat-style message array from a system prompt and a user prompt (with optional context variables), then returns the model’s text response. The node auto-fetches available Gemini models from the service and falls back to a curated list if the service is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgemini.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need a Gemini-family LLM to generate, analyze, summarize, or transform text. Choose a model, set a system prompt to control tone and behavior, and write a user prompt. Optionally inject up to four context strings into the prompt using {{input_1}}–{{input_4}} placeholders. Temperature and max_tokens control creativity and response length. Connect this node after upstream nodes that produce context or knowledge and pass those into input_1–input_4.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the Gemini model to use. Options are fetched from the LiteLLM service; if unavailable, a fallback list is shown.</td><td style="word-wrap: break-word;">gemini-2.5-pro</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Instructions that set behavior, style, or constraints for the model. If left blank, only the user prompt is sent.</td><td style="word-wrap: break-word;">Respond concisely and use bullet points where helpful.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The user message. Supports placeholders {{input_1}}–{{input_4}} that are replaced with the optional inputs.</td><td style="word-wrap: break-word;">Summarize the following notes: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness/creativity. Higher values produce more varied outputs.</td><td style="word-wrap: break-word;">0.7</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Upper bound on response length in tokens.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Use via {{input_1}} in the prompt.</td><td style="word-wrap: break-word;">Meeting notes from 2025-10-05 ...</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Use via {{input_2}} in the prompt.</td><td style="word-wrap: break-word;">Product specs v3.2 ...</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Use via {{input_3}} in the prompt.</td><td style="word-wrap: break-word;">Customer email thread ...</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string. Use via {{input_4}} in the prompt.</td><td style="word-wrap: break-word;">Support ticket #12345 details ...</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text response returned by the selected Gemini model.</td><td style="word-wrap: break-word;">Here is a concise summary of your notes...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model options are cached per provider for approximately 1 hour; if live fetching fails, a built-in fallback model list is used.
- Use {{input_1}}–{{input_4}} exactly in the prompt to inject the optional inputs; unmatched placeholders remain unchanged.
- Temperature and max_tokens are passed to the model as generation parameters and directly influence style and length.
- A sensible default system prompt is provided; clear, specific prompts with relevant context produce the best results.
- If a selected model is not in the available list, the node will raise an error prompting you to choose a valid option.

## Troubleshooting
- Model not found: Ensure the selected model name matches an available option. Refresh the model list or pick from the dropdown.
- Service request failed: Network or service issues can cause failures. Retry later or use a different model. The node will fall back to a predefined model list for selection, but requests still require service availability.
- Empty or overly short output: Increase max_tokens or reduce prompt constraints. Provide more context via input_1–input_4.
- Incoherent or too-random responses: Lower the temperature to make outputs more deterministic.
- Placeholders not replaced: Verify you used {{input_1}}–{{input_4}} syntax in the prompt and provided corresponding input values.
