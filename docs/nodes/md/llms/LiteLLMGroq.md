# Groq LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends a chat-style prompt to Groq-hosted language models through the Salt LLM service and returns the model’s reply as text. The node automatically lists available Groq models from the service (and falls back to built-in mappings if the service list is unavailable) and supports dynamic prompt composition using optional context inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/LiteLLMGroq.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to generate or transform text with Groq models. Select a model, set the system prompt (behavior/rules), compose the user prompt (optionally inserting {{input_1}}–{{input_4}}), and adjust temperature and max token limits. Commonly placed after retrieval or data-prep nodes to inject relevant context via input_1–input_4.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Groq model to use. A selectable list is fetched from the LLM service; if unavailable, a fallback list is provided.</td><td style="word-wrap: break-word;">llama-3.1-8b-instant</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions that shape the assistant’s behavior and tone. Leave empty to omit a system message.</td><td style="word-wrap: break-word;">You are a concise assistant. Provide step-by-step reasoning only when asked.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">User message to the model. Supports placeholders {{input_1}}–{{input_4}} that are replaced with the optional inputs below.</td><td style="word-wrap: break-word;">Summarize the following research notes: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness/creativity. Lower is more deterministic; higher is more diverse.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate for the response.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context inserted via {{input_1}} in the prompt.</td><td style="word-wrap: break-word;">Key findings from report A...</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context inserted via {{input_2}} in the prompt.</td><td style="word-wrap: break-word;">Customer feedback highlights...</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context inserted via {{input_3}} in the prompt.</td><td style="word-wrap: break-word;">Product specs v2.1</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context inserted via {{input_4}} in the prompt.</td><td style="word-wrap: break-word;">Constraints: budget <= $50k</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text reply from the selected Groq model.</td><td style="word-wrap: break-word;">Here is a concise summary of the provided notes...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model list resolution: the node queries the LLM service for available Groq models and caches them per provider. If that fails, it uses a built-in fallback list.
- Supported prompt placeholders: {{input_1}}, {{input_2}}, {{input_3}}, {{input_4}} are replaced with the corresponding optional inputs.
- Default behavior: if a non-empty system_prompt is provided, it is added as the system message; otherwise only the user prompt is sent.
- Timeouts: default request timeout is 90 seconds. Long-running models may require increasing this if supported by your environment.
- If you select a model name not recognized by the service/fallback list, the node will raise an error.
- This node is counted under LLM-type limits in project-level usage controls.

## Troubleshooting
- Model not found error: Choose a model from the provided dropdown list. If the list seems incomplete, retry after the service refreshes or use one from the fallback set.
- Empty or unexpected response: Verify the prompt content resolves correctly (placeholders replaced) and that max_tokens is high enough.
- Timeouts or failures: Increase the timeout if supported by the environment and ensure the LLM service endpoint is reachable.
- Low-quality outputs: Reduce temperature for more deterministic responses, or provide more explicit instructions/context in system_prompt and prompt.
