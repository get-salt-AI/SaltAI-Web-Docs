# Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node executes an iterative AI agent workflow using a selected language model, optional tools, optional knowledge bases, and optional conversation memory. It can automatically inject knowledge-base context, call configured tools across multiple reasoning rounds, and stop when it reaches a final answer or the maximum iteration limit. It also supports JSON-formatted output, fallback models, and optional PII redaction guardrails.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/ai/agents/saltagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need multi-step reasoning instead of a single prompt-response exchange, especially for workflows that must look up information, use external tools, or preserve conversational context across runs. It typically sits after prompt-building or data-preparation nodes that feed values into `context_data`, and before downstream consumers that parse the `response`, inspect `tool_calls_log`, or reuse `conversation_history` in looped agent patterns. It works well with upstream nodes that provide structured inputs, user instructions, or resource descriptions, and with downstream nodes that parse JSON outputs, display agent responses, branch on `is_complete`, or feed `conversation_history` back for loop-body execution. Best practice is to start with a strong `system_prompt`, keep `max_iterations` modest, enable `json_mode` only when a machine-readable result is required, and configure only the tools and knowledge bases the agent truly needs to reduce latency, cost, and tool misuse.

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
<tr><td style="word-wrap: break-word;">user_prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The main task, instruction, or question for the agent. It supports dynamic text and can reference values coming from connected upstream inputs after context substitution.</td><td style="word-wrap: break-word;">Summarize the support ticket history for customer {{ticket.customer_name}} and suggest the next best action.</td></tr>
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">The primary language model used for reasoning and tool calling. Available options come from the configured model catalog.</td><td style="word-wrap: break-word;">gpt-4.1</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens the model may generate. `0` removes the output cap; larger values increase cost and response size.</td><td style="word-wrap: break-word;">4096</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>False</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">High-level instructions that define the agent’s role, rules, tone, and operating constraints. If left blank, a default helpful-assistant prompt is used.</td><td style="word-wrap: break-word;">You are a customer operations agent. Use tools when needed, cite retrieved facts, and avoid inventing account details.</td></tr>
<tr><td style="word-wrap: break-word;">context_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional upstream data passed into the node for prompt-variable substitution. Supports multiple connections and can provide a structured substitution map or a single plain value.</td><td style="word-wrap: break-word;">{"ticket.id":"TCK-1042","ticket.customer_name":"Northwind Logistics","ticket.priority":"high"}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls response variability. Lower values are more deterministic; higher values are more creative but less consistent.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">top_p</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional sampling control that narrows token selection by cumulative probability. Best used instead of, not alongside, aggressive temperature tuning.</td><td style="word-wrap: break-word;">0.9</td></tr>
<tr><td style="word-wrap: break-word;">top_k</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Optional sampling limit that restricts candidate tokens to the top K choices. Not supported by every model provider.</td><td style="word-wrap: break-word;">40</td></tr>
<tr><td style="word-wrap: break-word;">frequency_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional repetition penalty that discourages repeated wording. Useful when the agent tends to reuse the same phrases.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">presence_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional novelty penalty that encourages the model to introduce new topics instead of repeating the same ones.</td><td style="word-wrap: break-word;">0.1</td></tr>
<tr><td style="word-wrap: break-word;">max_iterations</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of reasoning/tool-call rounds the agent may perform before stopping. Higher values allow deeper workflows but increase latency and cost.</td><td style="word-wrap: break-word;">6</td></tr>
<tr><td style="word-wrap: break-word;">memory_window_size</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many prior user/assistant message pairs to retain across runs for the same workflow and node context. `0` disables memory.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">enable_guardrails</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When enabled, the node detects and redacts common PII patterns such as email addresses, phone numbers, SSNs, and credit card numbers in inputs and outputs.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">json_mode</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Forces the final answer into valid JSON. Use this when downstream nodes need machine-readable output instead of prose.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">response_schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional JSON schema describing the desired output shape when JSON mode is enabled. The agent is instructed to conform its final response to this schema.</td><td style="word-wrap: break-word;">{"type":"object","properties":{"summary":{"type":"string"},"priority":{"type":"string"},"reasoning":{"type":"string"}},"required":["summary","priority","reasoning"]}</td></tr>
<tr><td style="word-wrap: break-word;">max_kb_context_chars</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Character budget for auto-injected knowledge-base context. Larger values provide more retrieved material but consume more prompt space.</td><td style="word-wrap: break-word;">50000</td></tr>
<tr><td style="word-wrap: break-word;">fallback_model</td><td>False</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Backup model automatically used if the primary model call fails. Useful for resilience in production workflows.</td><td style="word-wrap: break-word;">claude-opus-4-6</td></tr>
<tr><td style="word-wrap: break-word;">tools_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON configuration describing the tools the agent may call. This is typically managed through the node’s tools panel rather than edited manually.</td><td style="word-wrap: break-word;">[{"tool_id":"slack_send_message","name":"Send Slack Message","description":"Post a message to a Slack channel"}]</td></tr>
<tr><td style="word-wrap: break-word;">knowledge_bases_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON list of knowledge bases the agent is allowed to search. This is usually managed through the knowledge-base panel.</td><td style="word-wrap: break-word;">[{"kb_id":"kb-support-playbooks","name":"Support Playbooks"}]</td></tr>
<tr><td style="word-wrap: break-word;">encrypted_credentials_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Backend-injected encrypted credential map used for authorized tool execution. Typically not set manually.</td><td style="word-wrap: break-word;">{"cred-123":"<encrypted-credential-blob>"}</td></tr>
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
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The agent’s current or final response text. In JSON mode, this is returned as valid JSON text; otherwise it is natural-language output.</td><td style="word-wrap: break-word;">{"summary":"Customer issue is caused by expired API credentials.","priority":"high","reasoning":"KB guidance and tool results both indicate authentication failure."}</td></tr>
<tr><td style="word-wrap: break-word;">tool_calls_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded log of tool calls made during execution, including iteration number, arguments, and tool results. Useful for auditing and debugging.</td><td style="word-wrap: break-word;">[{"iteration":1,"tool_id":"search_knowledge_base","arguments":"{\"query\":\"API credential expiration\",\"knowledge_base_id\":\"kb-support-playbooks\"}","result":"{\"results\":[{\"title\":\"Credential Rotation\",\"content\":\"Rotate tokens every 90 days...\"}],\"result_count\":1}"}]</td></tr>
<tr><td style="word-wrap: break-word;">reasoning_history</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded record of each reasoning iteration, including whether the step produced tool calls or a final answer.</td><td style="word-wrap: break-word;">[{"iteration":1,"reasoning":"I should check the support playbook before answering.","action":"tool_call","tools_called":["search_knowledge_base"],"model":"gpt-4.1"}]</td></tr>
<tr><td style="word-wrap: break-word;">is_complete</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the agent finished with a final answer. `false` usually means it hit the iteration limit or is being driven step-by-step in a loop-body pattern.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">conversation_history</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded message history representing the current conversation state. Feed this back into the node to continue an iterative or loop-driven session.</td><td style="word-wrap: break-word;">[{"role":"system","content":"You are a customer operations agent."},{"role":"user","content":"Check invoice INV-2048."},{"role":"assistant","content":"I’ll look that up.","tool_calls":[{"id":"call_1","function":{"name":"billing_lookup","arguments":"{\"invoice_id\":\"INV-2048\"}"}}]},{"role":"tool","tool_call_id":"call_1","content":"{\"status\":\"overdue\"}"}]</td></tr>
<tr><td style="word-wrap: break-word;">error</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Execution error or warning text. This may contain iteration-limit notices, model/tool failures, or PII detection warnings when guardrails are enabled.</td><td style="word-wrap: break-word;">Agent reached max iterations (6)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each iteration may trigger one model call plus multiple tool or knowledge-base requests, so latency and cost grow quickly with higher `max_iterations` and large context injection budgets.
- **Behavior**: When `conversation_history` is supplied, the node switches to single-iteration loop-body behavior and expects an external loop to drive repeated execution.
- **Behavior**: Memory only stores user and assistant text messages, not tool-call artifacts, so replayed history stays valid across runs.
- **Limitations**: Knowledge-base searches are restricted to the knowledge bases explicitly configured for this node, and they require a valid execution user identity for authorization checks.
- **Limitations**: `json_mode` improves machine-readability, but malformed model output may still be coerced into a fallback JSON envelope rather than perfectly matching your intended schema.
- **Behavior**: If guardrails are enabled, detected PII is redacted in both input and final output, and warnings are surfaced through the `error` output.

## Troubleshooting
- **Model not found**: If execution fails because the selected model is unavailable, refresh the available model catalog or choose a model that exists in the current environment. A valid `fallback_model` can reduce failures.
- **Invalid tools_json**: If the node returns an error about invalid tool configuration JSON, rebuild the tool setup from the tools panel rather than editing the JSON manually.
- **No knowledge-base results**: If KB queries return nothing, verify that the node has configured knowledge bases, the `salt_user_id` execution context is present, and the requested KB is one of the allowed IDs for this node.
- **Tool execution failed**: If a connector tool errors, check that the tool has a valid connection or credential attached and that the executing user is authorized to use it.
- **Agent reached max iterations**: If `is_complete` is `false` with an iteration-limit error, simplify the prompt, reduce unnecessary tools, or increase `max_iterations` carefully.
- **Unexpected plain-text JSON output**: If downstream parsing fails in JSON mode, ensure `response_schema` is valid JSON and that the task prompt clearly asks for structured fields that match the schema.
