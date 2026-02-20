# OpenAI LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Calls OpenAI chat/completions via Salt’s LiteLLM layer. You select an OpenAI model, provide prompts and generation settings, and the node returns the model’s text response. It includes built-in fallback mappings for common/legacy model names to help keep workflows working as models change over time.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmopenai.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need OpenAI-powered text generation in a workflow. Typical flow: prepare or construct your prompt (and optional system prompt), select an OpenAI model, tune temperature and max_tokens, then feed the output downstream (e.g., to parsers, tools, or display nodes). It is suited for general-purpose chat, writing, and reasoning tasks that require OpenAI models.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The OpenAI model to use. A dropdown of available models is provided; legacy names are auto-mapped to current equivalents when possible.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">System-level instruction that sets behavior and role for the model. Keep concise and consistent across turns.</td><td style="word-wrap: break-word;">You are a concise technical assistant.</td></tr>
<tr><td style="word-wrap: break-word;">prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main user prompt or message content sent to the model.</td><td style="word-wrap: break-word;">Summarize the following article in three bullet points.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness. Lower is more deterministic; higher is more creative.</td><td style="word-wrap: break-word;">0.7</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Upper bound on the number of tokens generated in the response. Must be within the selected model’s limits.</td><td style="word-wrap: break-word;">512</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional input that you can interpolate into your prompt template or concatenate upstream.</td><td style="word-wrap: break-word;">Customer profile JSON</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional input for prompt composition.</td><td style="word-wrap: break-word;">Knowledge base excerpt</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional input for prompt composition.</td><td style="word-wrap: break-word;">Previous conversation context</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional additional input for prompt composition.</td><td style="word-wrap: break-word;">Formatting instructions</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The OpenAI model's text response.</td><td style="word-wrap: break-word;">Here are three concise bullet points summarizing the article...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Model availability and naming can change; this node includes fallback mappings (e.g., GPT-4o mini → gpt-4o-mini) to reduce breakage, but you should periodically validate model selections.
- You must have a valid OpenAI API credential configured in your Salt environment or workspace settings for this node to run.
- max_tokens must respect the chosen model’s context window; if set too high, the service may error or truncate.
- Temperature interacts with prompts and model choice; for reproducibility, keep temperature low and prompts specific.
- Usage is subject to organization-level limits and quotas; heavy use may trigger rate limits.

## Troubleshooting
- Model not found or deprecated: Pick a currently listed model from the dropdown or rely on the node’s automatic legacy-to-current mapping where available.
- Authentication error: Ensure an OpenAI API key is configured in your Salt credentials store or environment variables (e.g., set <openai-api-key> in the appropriate location).
- Rate limit or quota exceeded: Reduce request frequency, lower token usage, or upgrade plan/quotas; consider batching or adding delays.
- max_tokens too large: Decrease max_tokens to fit within the model’s context window and your prompt length.
- Empty or low-quality output: Lower temperature for more deterministic results, refine the system_prompt and prompt, and provide more concrete instructions or examples.
