# Groq LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node routes text prompts to Groq-hosted LLMs through the legacy LiteLLM integration. It is a thin wrapper around a shared LiteLLM base node with the provider fixed to Groq. The node is deprecated and mainly retained for backward compatibility; new workflows should use the unified LLM nodes instead.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/litellmgroq.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use LiteLLMGroq only when you need to maintain or execute older workflows that were originally built around provider-specific LiteLLM nodes and explicitly target Groq. Place it after nodes that construct prompts or assemble context (for example SaltString, SaltObject, or previous LLM/tool calls) and before nodes that consume the generated text, such as routing, parsing, or UI delivery nodes. A common pattern is: upstream nodes collect user input and system instructions, these are passed to LiteLLMGroq as user_prompt and context_data, and the resulting response_text is then analyzed or displayed downstream. For any new or actively maintained pipelines, prefer SaltLLM or SaltDeployedModel, which consolidate provider handling and ongoing feature support; internal mapping logic exists to replace LiteLLMGroq with these unified nodes while preserving prompt and context wiring. Ensure Groq credentials and default model configuration are correctly set in the environment or connection used by LiteLLM, because this node does not manage authentication or provider configuration itself.

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
<tr><td style="word-wrap: break-word;">user_prompt</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary user prompt or instruction sent to the Groq LLM. It should contain the specific question or task description and any inlined context that upstream nodes have already merged. Keep overall length within typical LLM context limits to avoid truncation or errors.</td><td style="word-wrap: break-word;">Draft a concise email to a customer explaining the root cause of yesterday's outage and the steps we are taking to prevent recurrence.</td></tr>
<tr><td style="word-wrap: break-word;">context_data</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional contextual or system-level information that is combined with the user prompt, such as role instructions, conversation history, or results from earlier tools. In many legacy LiteLLM workflows, multiple separate context fields (like system_prompt or input_1–input_4) are effectively merged into this stream.</td><td style="word-wrap: break-word;">You are a senior reliability engineer. Use clear, non-technical language whenever possible, but do not hide important technical details. Keep the tone transparent and empathetic.</td></tr>
<tr><td style="word-wrap: break-word;">model</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Groq model identifier to use for this call, overriding the environment default. Must correspond to a valid model configured for your Groq account; if omitted, the Groq default specified in your LiteLLM or workspace configuration is used.</td><td style="word-wrap: break-word;">groq/llama3-70b-8192</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Sampling temperature forwarded to Groq via LiteLLM. Lower values (0.0–0.3) yield more deterministic, focused responses; higher values (0.7–1.0) produce more varied, creative outputs. Values are typically expected to be within the 0–2 range.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens to generate in the completion. If not set, the provider or environment defaults are applied. Higher values allow longer responses but increase latency and token usage, whereas values that are too low can lead to incomplete answers.</td><td style="word-wrap: break-word;">400</td></tr>
<tr><td style="word-wrap: break-word;">stop_sequences</td><td>False</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">List of strings that tell the model when to stop generating further tokens. These are passed through LiteLLM to the Groq API and used to cut off the response as soon as any of the sequences appears.</td><td style="word-wrap: break-word;">['\nUser:', '<END_RESPONSE>']</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td>False</td><td style="word-wrap: break-word;">OBJECT</td><td style="word-wrap: break-word;">Optional metadata object attached to the request. Actual supported keys depend on your LiteLLM and Groq configuration; commonly used for logging, tracing, routing, or internal bookkeeping.</td><td style="word-wrap: break-word;">{'request_id': 'incident-2024-07-18-1245', 'tags': ['postmortem', 'customer-facing'], 'owner_team': 'platform-reliability'}</td></tr>
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
<tr><td style="word-wrap: break-word;">response_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The main text content generated by the Groq LLM. This is usually what you show to end users or feed into downstream parsing, classification, or routing nodes.</td><td style="word-wrap: break-word;">Hello Sarah,  Yesterday's outage was caused by a misconfiguration in our database connection pool that led to resource exhaustion under peak load. We have already corrected the configuration, added safeguards to prevent similar issues, and expanded our monitoring to catch early warning signs. We are also conducting a full review of our deployment procedures to avoid this type of incident in the future.  Thank you for your patience, The Platform Reliability Team</td></tr>
<tr><td style="word-wrap: break-word;">raw_response</td><td style="word-wrap: break-word;">OBJECT</td><td style="word-wrap: break-word;">Structured response object returned via LiteLLM for the Groq call, typically including the model name, choices/messages, token usage, and finish reason. Use this output when you need metadata such as token counts, provider name, or finish_reason for logging, cost tracking, or conditional branching.</td><td style="word-wrap: break-word;">{'id': 'cmpl-xyz789', 'model': 'groq/llama3-70b-8192', 'choices': [{'index': 0, 'finish_reason': 'stop', 'message': {'role': 'assistant', 'content': "Hello Sarah,\n\nYesterday's outage was caused by a misconfiguration in our database connection pool that led to resource exhaustion under peak load. We have already corrected the configuration, added safeguards to prevent similar issues, and expanded our monitoring to catch early warning signs. We are also conducting a full review of our deployment procedures to avoid this type of incident in the future.\n\nThank you for your patience,\nThe Platform Reliability Team"}}], 'usage': {'prompt_tokens': 612, 'completion_tokens': 168, 'total_tokens': 780}, 'provider': 'groq'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Latency and throughput are influenced by both LiteLLM overhead and Groq’s API performance; large prompts or high max_tokens values can significantly increase response time and token consumption.
- **Limitations**: This node is categorized as Deprecated and is not the target for new features or improvements. Prefer SaltLLM or SaltDeployedModel for any new workflows or when refactoring existing ones.
- **Behavior**: The provider is hard-coded to "groq" within this node; switching to another provider requires replacing the node (or moving to a unified LLM node) rather than changing an input parameter.
- **Behavior**: In workspaces where pro-node limits are enforced, LiteLLMGroq counts as an LLM node toward the allowed quota of premium LLM nodes per workflow, which may restrict how many you can include.

## Troubleshooting
- **Common Error 1**: "Groq API key missing or invalid" – Confirm that Groq credentials are correctly set up in the environment or LiteLLM connection configuration. This node does not accept credentials directly as inputs, so fixing the central configuration is required.
- **Common Error 2**: "Model not found" or similar provider error – Check that the model input (if provided) matches a valid Groq model name and that your account has access to it. Clearing the model field will fall back to the default model configured for Groq.
- **Common Error 3**: Issues after migrating away from LiteLLMGroq – When replacing this node with SaltLLM or SaltDeployedModel, verify that user prompts and system/context instructions are connected to the correct new inputs (for example user_prompt and context_data) to avoid sending empty or malformed prompts.
- **Common Error 4**: Responses appear truncated or incomplete – Increase the max_tokens input or reduce the length of prompts and context_data. If available in raw_response, inspect the finish_reason field to see whether the generation stopped because of token limits or a stop sequence.
