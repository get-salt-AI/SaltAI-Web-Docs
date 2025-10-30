# Anthropic LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs prompts against Anthropic Claude models via the Salt LLM service. It supports dynamic prompt composition, model auto-discovery with graceful fallback, temperature control, and max token limits. Designed for general text generation tasks like summarization, Q&A, drafting, and analysis.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/LLMs/LiteLLMAnthropic.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need text responses from Anthropic models. Provide a system prompt to control tone and rules, a main prompt (which can include placeholders), and optionally connect up to four string inputs (e.g., retrieved knowledgebase content) to dynamically fill the prompt. Select a model from the dropdown; if the live model list isn't available, a curated fallback list is used. The node returns a single string with the model's response.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING (select)</td><td style="word-wrap: break-word;">Anthropic model to use. The list is fetched from the LLM service; if unavailable, a fallback list of Claude models is provided.</td><td style="word-wrap: break-word;">claude-3-5-sonnet-20241022</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Instructions and rules for the assistant’s behavior, tone, and format.</td><td style="word-wrap: break-word;">You are a helpful assistant. Answer concisely and cite sources when possible.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">User-facing request. Supports placeholders for connected inputs using {{input_1}}..{{input_4}}.</td><td style="word-wrap: break-word;">Summarize the following research notes in 5 bullet points: {{input_1}}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls creativity and variability. Lower = more deterministic; higher = more diverse.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum tokens to generate. Influences response length.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input. Refer to it in the prompt as {{input_1}}.</td><td style="word-wrap: break-word;">Long-form notes or retrieved context text</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input. Refer to it in the prompt as {{input_2}}.</td><td style="word-wrap: break-word;">Additional document text</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input. Refer to it in the prompt as {{input_3}}.</td><td style="word-wrap: break-word;">Knowledgebase snippet</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual input. Refer to it in the prompt as {{input_4}}.</td><td style="word-wrap: break-word;">User profile or constraints</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated text from the selected Anthropic model.</td><td style="word-wrap: break-word;">Here is a concise, 5-bullet summary of the provided notes...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Model selection**: The node queries the live Anthropic model list; if unavailable, it falls back to known Claude models (e.g., claude-3-7-sonnet-20250219, claude-3-5-sonnet-20241022, claude-3-haiku-20240307).
- **Dynamic placeholders**: Use {{input_1}} to {{input_4}} inside the prompt to inject optional inputs.
- **Response length**: max_tokens bounds the completion length and may be trimmed by the service if too large.
- **Timeouts**: The node uses a request timeout (default 90s). Long-running requests may need a higher timeout configured upstream.
- **Required configuration**: The Salt LLM service must be configured with valid Anthropic API credentials for requests to succeed.

## Troubleshooting
- **Model ID not found**: Select a model from the dropdown. If you pasted a name, ensure it matches an available or fallback model.
- **Empty or very short output**: Increase max_tokens or reduce temperature; verify your prompt and system prompt are not empty and that placeholders are populated.
- **Request timed out**: Reduce prompt/context size, lower max_tokens, or adjust the service timeout configuration.
- **Inaccurate or off-topic responses**: Refine the system_prompt with clearer rules and add more specific examples/context via input_1..input_4.
- **Service error from LLM**: Verify Anthropic credentials and connectivity in the Salt LLM service; try another model from the list.
