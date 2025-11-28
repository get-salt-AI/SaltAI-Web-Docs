# Anthropic LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs prompts against Anthropic models via the LiteLLM integration. You provide a model, system prompt, user prompt, and generation settings; the node returns the generated text. Includes a built-in fallback mapping for common Anthropic model names.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmanthropic.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need text generation, reasoning, or assistant-style responses from Anthropic’s Claude family. Typical workflow: choose a Claude model, set a system prompt to define behavior, pass your prompt content (optionally composed from other node outputs via input_1–input_4), tune temperature and max_tokens, then feed the resulting text to downstream nodes for parsing or display.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Anthropic model to use. If the service cannot resolve the exact name, the node uses an internal fallback mapping for well-known Claude models.</td><td style="word-wrap: break-word;">claude-3-7-sonnet-20250219</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">System instructions that steer the model’s role, tone, and constraints.</td><td style="word-wrap: break-word;">You are a helpful assistant. Be concise and cite sources when possible.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Main user prompt content to send to the model. You can compose this with optional inputs.</td><td style="word-wrap: break-word;">Summarize the following report and list three key risks.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness. Lower is more deterministic; higher is more creative.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the response.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional extra text input for composing prompts (e.g., context, retrieved passages).</td><td style="word-wrap: break-word;">Previous conversation transcript</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional extra text input for composing prompts.</td><td style="word-wrap: break-word;">User profile or preferences</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional extra text input for composing prompts.</td><td style="word-wrap: break-word;">Knowledge base excerpt</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional extra text input for composing prompts.</td><td style="word-wrap: break-word;">Structured context or instructions</td></tr>
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
<tr><td style="word-wrap: break-word;">Output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The model’s generated text output.</td><td style="word-wrap: break-word;">Here’s a concise summary of the report along with three key risks...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model options include common Anthropic Claude variants and are backed by an internal fallback map. If a model alias is used, it resolves to a provider-qualified name like anthropic/claude-... depending on availability.
- Token limits and supported parameters can vary by model. Ensure max_tokens fits within the model’s context window together with your input size.
- Temperature meaning: lower values yield more focused, repeatable outputs; higher values increase creativity and variability.
- Optional inputs (input_1–input_4) are not merged automatically; construct your final prompt string accordingly in the prompt field if you need to include them.
- Access to Anthropic requires proper service configuration and credentials managed by the platform; ensure they are set by your administrator.
- This node returns a single STRING response; if you need structured output, instruct the model to format JSON and parse it in subsequent nodes.

## Troubleshooting
- Invalid model name or unsupported version: Pick a model from the available options or use one of the mapped Claude names (e.g., claude-3-7-sonnet-20250219).
- Authorization or key errors: Verify that Anthropic access is configured by your administrator. The node itself does not accept keys directly.
- Context length or token limit exceeded: Reduce prompt size, lower max_tokens, or choose a model with a larger context window.
- Empty or low-quality outputs: Decrease temperature for more deterministic results, refine the system_prompt, and provide clearer instructions.
- Rate limits or timeouts: Retry with backoff, reduce request frequency, or simplify prompts to lower latency.
