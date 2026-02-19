# Gemini LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes Large Language Model calls to Google’s Gemini models through the LiteLLM provider layer. The node selects the Gemini backend (provider="gemini") and supports multiple Gemini/Gemma model aliases via an internal fallback mapping to ensure compatibility with expected model names.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgemini.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node anywhere you need LLM-generated text or reasoning powered by Google Gemini within a workflow. Select a Gemini or Gemma model, provide your prompt/messages, and connect its output to downstream logic, parsing, or display nodes. It is typically paired with input nodes (for dynamic prompts) and output or inspection nodes (to capture responses).

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
<tr><td style="word-wrap: break-word;">model</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Model identifier to use. If a plain name is provided, the node maps it to a provider-qualified name when needed. Supported aliases include: gemini-2.5-flash, gemini-2.5-pro, gemini-2.0-flash, gemini-2.0-flash-001, gemini-2.0-flash-lite-001, gemini-2.0-flash-lite, gemma-3-1b-it, gemma-3-4b-it, gemma-3-12b-it, gemma-3n-e4b-it, gemma-3n-e2b-it, gemini-flash-latest, gemini-flash-lite-latest, gemini-pro-latest, gemini-2.5-flash-lite.</td><td style="word-wrap: break-word;">gemini-2.5-flash</td></tr>
<tr><td style="word-wrap: break-word;">prompt_or_messages</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The text prompt or chat messages to send to the model. Exact structure depends on the base LLM interface.</td><td style="word-wrap: break-word;">Write a short haiku about the ocean.</td></tr>
<tr><td style="word-wrap: break-word;">generation_parameters</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional tuning parameters such as temperature, max tokens, and top_p. Specific names and ranges depend on the underlying base node.</td><td style="word-wrap: break-word;">{"temperature": 0.7, "max_tokens": 256}</td></tr>
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
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The model’s response payload (usually text; exact structure depends on the base LiteLLM node).</td><td style="word-wrap: break-word;">The ocean whispers blue horizons stretch afar secrets in the deep</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is backed by LiteLLM with provider set to "gemini".
- It supports a fallback model mapping so you can use simpler names (e.g., "gemini-2.5-flash") which are internally mapped to provider-qualified forms (e.g., "gemini/gemini-2.5-flash").
- You must configure valid credentials for the Gemini provider in your environment or platform settings. Never hardcode secrets; use secure configuration (e.g., <gemini-api-key>).
- This node is counted under LLM usage limits in certain plan tiers. If your workflow reports limit errors, review your LLM node quota.
- Exact input/output shapes (prompt/messages format, return structure) are defined in the shared LiteLLM base implementation and may vary by platform version.

## Troubleshooting
- Model not recognized: Ensure the model name is one of the supported aliases or the fully-qualified provider form (e.g., "gemini/gemini-2.5-flash").
- Authentication error: Verify that a valid Gemini API key is configured in your environment (e.g., <gemini-api-key>) and that your account has access to the chosen model.
- Empty or truncated output: Increase max tokens (if supported) or reduce prompt size. Check any rate/usage limits.
- Rate limit or quota errors: Reduce request frequency, choose a lighter model, or upgrade your plan to increase LLM quotas.
- Unexpected response structure: Confirm downstream nodes expect the correct format and that you are using compatible parameters supported by the base LiteLLM interface.
