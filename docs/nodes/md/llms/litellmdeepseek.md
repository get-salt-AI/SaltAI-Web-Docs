# DeepSeek LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes Large Language Model calls to the DeepSeek provider via the LiteLLM integration. It normalizes model names and provides fallbacks for common DeepSeek models (chat, coder, reasoner) to ensure compatibility even if display names change. Behavior such as request formatting, invocation, and response handling is inherited from the shared LiteLLM base.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmdeepseek.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to invoke DeepSeek models for general chat, code-focused assistance, or reasoning-style outputs within a workflow. Select an available DeepSeek model and pass your prompt/messages; the node will route the request to the DeepSeek API and return the model’s response for downstream processing or display.

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
<tr><td style="word-wrap: break-word;">model</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The DeepSeek model to use. Legacy display names are remapped to supported identifiers when possible. Supported fallbacks include deepseek-chat, deepseek-coder, and deepseek-reasoner.</td><td style="word-wrap: break-word;">deepseek/deepseek-reasoner</td></tr>
<tr><td style="word-wrap: break-word;">prompt_or_messages</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Text prompt or structured messages for the model, depending on how your workflow constructs LLM requests.</td><td style="word-wrap: break-word;">Explain the difference between BFS and DFS in simple terms.</td></tr>
<tr><td style="word-wrap: break-word;">generation_params</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Optional generation parameters inherited from the LiteLLM base (for example, temperature, max tokens, top_p, etc.). Exact fields depend on the shared base configuration.</td><td style="word-wrap: break-word;">{"temperature": 0.7, "max_tokens": 512}</td></tr>
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
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">The DeepSeek model’s response object or text, as returned by the shared LiteLLM base.</td><td style="word-wrap: break-word;">A concise answer string or a structured response object with message content and optional usage metadata.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Fallback models are provided for common DeepSeek variants: deepseek-chat, deepseek-coder, deepseek-reasoner.
- Model name normalization may occur (e.g., legacy or display names can be mapped to currently supported identifiers).
- Credentials and API access are required for DeepSeek; ensure the appropriate API key or connection is configured in your environment.
- This node is part of the LLM category and may be subject to usage limits enforced by your workspace or environment.
- All request/response formatting, streaming behavior, and error handling are defined by the shared LiteLLM base for consistency across providers.

## Troubleshooting
- Model not found or unsupported: Select one of the supported DeepSeek models (e.g., deepseek/deepseek-reasoner, deepseek/deepseek-chat, deepseek/deepseek-coder) or ensure legacy names are mapped correctly.
- Authentication errors: Verify that your DeepSeek credentials are configured and valid for the environment.
- Timeouts or rate limits: Reduce request size (e.g., max tokens) or retry after waiting; check provider rate limits.
- Unexpected response format: Confirm downstream nodes expect the same response shape produced by the shared LiteLLM base, and adjust parsing accordingly.
