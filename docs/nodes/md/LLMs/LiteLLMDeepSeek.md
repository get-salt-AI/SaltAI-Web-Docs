# DeepSeek LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries DeepSeek language models via the LiteLLM service. Builds a message list from a system prompt and a user prompt (with optional dynamic placeholders), then sends it to the DeepSeek provider and returns the model’s text reply.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/LLMs/LiteLLMDeepSeek.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to call DeepSeek models for text generation, reasoning, or coding tasks. Choose a model, provide a system prompt to guide behavior, write your prompt (optionally referencing input_1..input_4), and tune temperature and max_tokens. Connect upstream nodes to input_1..input_4 to inject dynamic context, such as retrieved knowledge or user data.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">DeepSeek model to use. The list is fetched from the service; if unavailable, fallback options like deepseek-coder and deepseek-reasoner are provided.</td><td style="word-wrap: break-word;">deepseek-coder</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">High-level instructions and rules for how the model should respond. Leave empty to skip a system message.</td><td style="word-wrap: break-word;">You are a concise assistant. Prefer clear bullet points.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Main user prompt. Supports placeholder variables {{input_1}}..{{input_4}} that are replaced with the connected inputs before sending.</td><td style="word-wrap: break-word;">Summarize the following notes: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls response randomness/creativity from 0 to 1.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the response.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input available to the prompt as {{input_1}}.</td><td style="word-wrap: break-word;">Meeting notes text</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input available to the prompt as {{input_2}}.</td><td style="word-wrap: break-word;">Project requirements</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input available to the prompt as {{input_3}}.</td><td style="word-wrap: break-word;">Code snippet</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional dynamic input available to the prompt as {{input_4}}.</td><td style="word-wrap: break-word;">Customer message</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text message returned by the DeepSeek model.</td><td style="word-wrap: break-word;">Here is a concise summary of your notes...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model sourcing**: The node attempts to fetch available DeepSeek models from the service. If it fails, it falls back to predefined options (e.g., deepseek-coder, deepseek-reasoner).
- **Dynamic variables**: Only {{input_1}}..{{input_4}} are replaced. Other placeholders will remain unchanged.
- **Timeouts**: Long-running requests are supported and will keep a heartbeat; default timeout is 90 seconds unless overridden by system configuration.
- **Max tokens**: If the response would exceed max_tokens, it may be truncated. Adjust as needed for longer outputs.
- **Model validation**: Selecting a model not present in the fetched/fallback list will cause an error.

## Troubleshooting
- **Model ID not found**: Ensure the selected model exists in the model list. If the live list fails, use one of the fallback models.
- **Service error or timeout**: Check connectivity and credentials for the DeepSeek provider in your environment. Increase the timeout if supported in your setup.
- **Empty or poor responses**: Lower temperature for more deterministic outputs, or increase max_tokens to avoid truncation.
- **Placeholders not replaced**: Verify that input_1..input_4 are connected and that your prompt uses the correct placeholder format (e.g., {{input_1}}).
- **Rate limits or provider errors**: Try a different model, slow down request frequency, or validate account limits with the provider.
