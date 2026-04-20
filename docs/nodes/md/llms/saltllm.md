# LLM

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltLLM runs a single large language model completion against any model exposed via the platform’s LLM service. It combines a user prompt, optional system prompt, and optional upstream context into a messages payload, then calls the selected model with configurable parameters like temperature, max tokens, and penalties. It can also enforce JSON responses that follow a given schema and automatically fall back to a backup model if the primary call fails.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/llms/saltllm.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltLLM whenever you need a straightforward LLM call—answering questions, rewriting content, generating code, or returning structured data—without agent loops, tools, or memory. Place it after content- or data-producing nodes (for example SaltInput, SaltList, data loaders, or parsing nodes) and connect those outputs to the context_data input; then reference them in system_prompt or user_prompt via the variable picker. Typical workflows are: user input → preprocessing nodes (such as templating or retrieval) → SaltLLM → response post-processing (for example parsing JSON, routing, or sending to SaltOutput). Combine it with nodes that manage external data (retrievers, database or API nodes) for retrieval-augmented generation, or with conditional or routing nodes to branch logic based on the model’s response. Prefer json_mode and a clear response_schema whenever downstream nodes expect structured data; keep max_tokens sized to your longest expected answer and use fallback_model for resilience in production flows. Use provider- or model-specific tuning (temperature, top_p, top_k, penalties) sparingly and favor defaults unless you have a clear behavioral goal.

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
<tr><td style="word-wrap: break-word;">user_prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Natural language instructions or request to the model. You can interpolate variables from connected nodes using the variable picker. Must be a string; keep it focused and explicit about the task, format, and constraints.</td><td style="word-wrap: break-word;">You are helping a sales rep. Using {{lead_info.name}} and {{lead_info.company}}, draft a short, friendly outreach email in 120–180 words.</td></tr>
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">MODEL_SELECTOR</td><td style="word-wrap: break-word;">Name of the model to call, selected from all models configured in the platform’s LLM service. Appears as a dropdown grouped by provider. Behind the scenes, this is resolved to a provider-specific model ID.</td><td style="word-wrap: break-word;">openai/gpt-4o-mini</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens the model may generate. Range 0–128000, slider step 256. Higher values allow longer responses but consume more quota and may increase latency.</td><td style="word-wrap: break-word;">2048</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>False</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Optional system-level instructions defining role, tone, and rules. If empty, a default system prompt is injected. Variables from context can be referenced. Use this to set global behavior rather than task details.</td><td style="word-wrap: break-word;">You are a precise legal assistant. Always respond in clear, numbered bullet points and avoid speculative claims.</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness of the output. Range 0–2, default 0.7, slider step 0.1. Lower values (0–0.3) yield more deterministic answers; higher values increase creativity and variation.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">context_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Generic multi-connect input for upstream node outputs. These values are parsed into a context object used for variable substitution inside prompts (for example, {{node_1.field}}). Can accept lists, strings, objects, or other node outputs.</td><td style="word-wrap: break-word;">Connection from a SearchDocuments node providing a list of retrieved passages and from a UserProfile node providing {"name": "Alex", "plan": "Pro"}.</td></tr>
<tr><td style="word-wrap: break-word;">top_p</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Nucleus sampling parameter (0–1, default None, slider step 0.05). Limits token choices to a subset whose cumulative probability is less than or equal to top_p. Commonly used instead of temperature; some providers may ignore it.</td><td style="word-wrap: break-word;">0.9</td></tr>
<tr><td style="word-wrap: break-word;">top_k</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Limits sampling to the top K most likely next tokens. Range 1–500, default None. Only some providers and models support this; when unsupported it is ignored.</td><td style="word-wrap: break-word;">40</td></tr>
<tr><td style="word-wrap: break-word;">frequency_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Reduces repetition of tokens based on their frequency in the text. Range -2 to 2, default None, step 0.1. Small positive values (0.1–0.5) discourage repetitive phrasing.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">presence_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Penalizes tokens simply for having appeared before, encouraging exploration of new topics. Range -2 to 2, default None, step 0.1.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">json_mode</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, the node instructs the model to return pure JSON with no markdown or extra text. Often combined with response_schema so downstream nodes can safely parse the result.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">response_schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional JSON schema or example object describing the shape of the desired JSON output. Used only when json_mode is true; injected into the system prompt as a structured format guideline.</td><td style="word-wrap: break-word;">{"summary": "string", "sentiment": "positive \| neutral \| negative", "key_points": ["string"]}</td></tr>
<tr><td style="word-wrap: break-word;">fallback_model</td><td>False</td><td style="word-wrap: break-word;">MODEL_SELECTOR</td><td style="word-wrap: break-word;">Optional backup model to call automatically if the primary model fails, for example due to rate limits or provider outage. Uses the same prompts and tuning parameters, with normalization adjusted for the fallback model.</td><td style="word-wrap: break-word;">anthropic/claude-3-haiku</td></tr>
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
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The primary text output from the LLM. In normal mode this is free-form text such as answers, email drafts, or explanations. When json_mode is enabled, it is a JSON string, potentially normalized to valid JSON, matching the provided response_schema.</td><td style="word-wrap: break-word;">{"summary": "User is asking about upgrading from Basic to Pro to access team features.", "sentiment": "positive", "key_points": ["Interested in Pro plan", "Wants team collaboration features", "Concerned about pricing flexibility"]}</td></tr>
<tr><td style="word-wrap: break-word;">error</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Error message field. Under normal successful runs this is an empty string. When a handled failure occurs before a hard exception, this may contain a user- or system-readable error message; however, for most fatal failures, the node raises an exception instead of emitting a non-empty error output.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large max_tokens values on long prompts can significantly increase latency and token usage; size this based on your maximum expected answer length and consider shorter prompts for high-throughput workflows.
- **Limitations**: This node performs a single LLM call with no tools, long-term memory, or iterative planning; for multi-step reasoning with tool calls or retrieval you should pair it with other orchestration nodes.
- **Behavior**: When json_mode is enabled, the node will still try to coerce slightly malformed responses by stripping markdown fences and validating JSON, but it cannot guarantee schema correctness beyond what the model produces.
- **Behavior**: If the primary model call fails and a different fallback_model is configured, the node transparently retries once with that fallback; if both calls fail it raises an exception and the workflow run will typically stop.
- **Behavior**: Optional parameters such as temperature, top_p, top_k, frequency_penalty, and presence_penalty are normalized per provider and model; values outside supported ranges may be clamped or dropped based on provider-specific rules.
- **Limitations**: Some providers do not support top_k or certain penalty parameters; in those cases the settings may be ignored even though they are accepted by the node.

## Troubleshooting
- **Primary model failures**: If you see run failures with messages beginning with "LLM call failed:" and no usable response, set a fallback_model from a different provider or smaller model family and re-run. Also verify that the selected model is still available in your organization.
- **Invalid JSON in json_mode**: When downstream nodes fail to parse the response as JSON, confirm that json_mode is enabled and that response_schema is a valid, non-empty JSON object or clearly structured example. Tighten instructions in system_prompt to emphasize strict JSON output only.
- **Unexpected or empty outputs**: If the response is irrelevant or too short, review the user_prompt and system_prompt for clarity, ensure the right variables are referenced from context_data, and increase max_tokens so the model has space to answer fully.
- **High variability or hallucinations**: If the model makes up information or answers inconsistently, lower temperature into the 0–0.3 range, avoid using both temperature and top_p at aggressive values, and provide more concrete guidance and examples in the prompts.
- **Provider-specific parameter issues**: If logs indicate parameter errors or some controls appear ineffective, try removing top_k, top_p, or penalties to isolate which one is unsupported by the chosen model, then adjust using values known to be supported by that provider.
