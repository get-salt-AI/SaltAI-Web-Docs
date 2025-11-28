# DeepSeek LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a chat-style Large Language Model request using the DeepSeek provider via the LiteLLM gateway. Supports dynamic prompt composition, system instructions, temperature, and max token control. Automatically lists available models from the LLM service and falls back to known DeepSeek model IDs if the service is unavailable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmdeepseek.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need DeepSeek models to generate or transform text in a workflow. Connect optional context inputs to enrich the prompt and reference them with placeholders inside the main prompt. Select the desired DeepSeek model, set creativity (temperature) and response length (max_tokens), then route the single text output to downstream nodes.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">The DeepSeek model to use. The node fetches available models from the LLM service and falls back to known DeepSeek models if listing fails.</td><td style="word-wrap: break-word;">deepseek-chat</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">System-level instructions that set behavior, tone, and formatting rules for the model.</td><td style="word-wrap: break-word;">You are a concise assistant. Respond with bullet points when appropriate.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The user prompt. Supports placeholders {{input_1}} to {{input_4}} that are replaced with the optional inputs if provided.</td><td style="word-wrap: break-word;">Summarize the following context: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls creativity and randomness. Lower is more deterministic; higher is more creative.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the response. Controls response length.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to merge into the prompt via {{input_1}}.</td><td style="word-wrap: break-word;">Quarterly financial report text...</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to merge into the prompt via {{input_2}}.</td><td style="word-wrap: break-word;">Customer feedback snippets</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to merge into the prompt via {{input_3}}.</td><td style="word-wrap: break-word;">Product specifications</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional context string to merge into the prompt via {{input_4}}.</td><td style="word-wrap: break-word;">Internal policy guidelines</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The model’s generated text response.</td><td style="word-wrap: break-word;">Here’s a concise summary of the provided context...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model choices are fetched and cached per provider. If fetching fails, the node falls back to known DeepSeek models: deepseek-chat, deepseek-coder, deepseek-reasoner.
- Use placeholders {{input_1}} to {{input_4}} inside the prompt to inject optional inputs; missing inputs are replaced with empty strings.
- Default request timeout is approximately 3 minutes; very long generations may require lowering max_tokens or adjusting settings upstream.
- This node contributes to LLM usage limits defined by your workspace or plan.

## Troubleshooting
- Model not found error: Ensure you select a model from the provided dropdown. If the list is empty, try using a known model like "deepseek-chat".
- Empty or unexpected output: Verify the main prompt is not blank and that placeholders (e.g., {{input_1}}) have corresponding inputs connected.
- Timeouts or slow responses: Reduce max_tokens, lower temperature for more deterministic output, or try a lighter model.
- Service unavailable or listing fails: The node will use fallback models. If requests still fail, try again later or check your network and service status.
- Formatting issues with placeholders: Make sure placeholders exactly match {{input_1}} to {{input_4}} syntax without extra spaces.
