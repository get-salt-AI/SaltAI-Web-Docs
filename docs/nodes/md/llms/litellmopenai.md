# OpenAI LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sends prompts to OpenAI language models through the Salt LiteLLM layer and returns the model’s response. Supports a wide range of current and legacy OpenAI model IDs with built-in fallback mapping. Designed to handle typical chat/completions use cases as well as newer OpenAI reasoning and small-footprint variants.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmopenai.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need text generation, chat-style responses, reasoning, or structured output from OpenAI models within a Salt workflow. Place it after any prompt-building or context-assembling nodes, then route its outputs to parsers, evaluators, or downstream application logic. Choose a model from the supported list (e.g., GPT-4o family, o-series, GPT-5 variants) and tune generation controls like temperature and max_tokens as needed.

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
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The OpenAI model identifier to use. Supports many current options and legacy names that are auto-mapped to current equivalents.</td><td style="word-wrap: break-word;">gpt-4o</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Your prompt or message content sent to the model. Typically a user/system combined prompt or a prepared message string.</td><td style="word-wrap: break-word;">Summarize the following article in 3 bullet points.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>False</td><td style="word-wrap: break-word;">NUMBER</td><td style="word-wrap: break-word;">Controls randomness. Higher values produce more diverse outputs.</td><td style="word-wrap: break-word;">0.7</td></tr>
<tr><td style="word-wrap: break-word;">top_p</td><td>False</td><td style="word-wrap: break-word;">NUMBER</td><td style="word-wrap: break-word;">Nucleus sampling parameter to control output diversity.</td><td style="word-wrap: break-word;">0.9</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>False</td><td style="word-wrap: break-word;">NUMBER</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the response.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional system-level instructions to steer the assistant’s behavior.</td><td style="word-wrap: break-word;">You are a concise assistant that answers with bullet points.</td></tr>
<tr><td style="word-wrap: break-word;">response_format</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional desired response format hint (e.g., plain text or JSON).</td><td style="word-wrap: break-word;">json</td></tr>
<tr><td style="word-wrap: break-word;">tools</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional tool/function definitions to enable tool calling if supported by the chosen model.</td><td style="word-wrap: break-word;">Not specified</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The primary text output from the model.</td><td style="word-wrap: break-word;">• Point 1 • Point 2 • Point 3</td></tr>
<tr><td style="word-wrap: break-word;">raw</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Full raw response payload with metadata such as token usage and model info.</td><td style="word-wrap: break-word;">{"id":"resp_abc123","model":"gpt-4o","usage":{"total_tokens":345},"output":"..."}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- • Provider is OpenAI. Display name appears as "OpenAI LLM" in the Salt UI.
- • Supported/fallback models include: gpt-4, gpt-3.5-turbo, gpt-4-turbo, gpt-4o, gpt-4o-mini, chatgpt-4o-latest, o1, o1-mini, o3, o3-mini, o4-mini, gpt-4.1, gpt-4.1-mini, gpt-4.1-nano, gpt-5, gpt-5-chat-latest, gpt-5-nano.
- • Some legacy model names are automatically mapped to current equivalents in the UI.
- • This node is subject to LLM usage limits in Salt (per plan/remote config). If you hit limits, you may be blocked from adding/running more LLM nodes.
- • An OpenAI API key must be configured in your Salt environment by an administrator; the node itself does not take a secret key input.

## Troubleshooting
- • Model not found or deprecated: Switch to a currently supported model (e.g., gpt-4o or gpt-4.1). The UI may auto-map some legacy names.
- • Rate limit or quota errors: Reduce request frequency, lower max_tokens, or wait for your quota window to reset. Verify your organization’s OpenAI billing and Salt plan limits.
- • Empty or low-quality output: Lower temperature for more deterministic results, provide clearer system instructions, or increase max_tokens.
- • Tool calling not working: Ensure the chosen model supports tools and that tool definitions are correctly structured. If issues persist, try another compatible model.
- • Node limit reached: Salt may restrict the number of LLM nodes per workflow/account tier. Remove unused LLM nodes or upgrade your plan.
