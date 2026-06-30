# Agent

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The Agent node sends your instructions to a selected LLM and can run a multi-step tool-calling loop to complete complex tasks. It can use configured connector tools, custom node tools, knowledge bases, conversation memory, guardrails, fallback models, and optional JSON output formatting.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/ai/agents/saltagent.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when a workflow needs reasoning, decision-making, or multi-step automation rather than a single text completion. Typical scenarios include answering questions from connected workflow data, summarizing documents with knowledge base context, deciding which SaaS connector tool to call, producing structured JSON for downstream automation, or coordinating several tools to complete a task.

Place Agent after upstream data-producing nodes and connect their outputs to `context_data`, then reference those values in `user_prompt` or `system_prompt` with the variable picker. Configure available tools in the Tools panel, usually using app/tool nodes such as `SaltToolNode` variants as the source of connector actions. Configure knowledge bases in the Knowledge Bases panel when the agent should retrieve relevant document context before answering.

Common downstream consumers are text processing nodes, JSON parsing or routing nodes, notification/action nodes, and loop nodes that use `conversation_history` to drive one agent iteration per loop cycle. Use `response` for the final answer, `is_complete` to branch on completion, `tool_calls_log` for audit/debug output, and `conversation_history` when building iterative agent workflows.

Best practices: keep `system_prompt` stable and rule-focused, put the task-specific request in `user_prompt`, expose only the tool inputs the model should decide, set static/manual tool inputs in the tool configuration, keep `max_iterations` as low as practical, enable JSON Output with a schema when downstream nodes require structured data, and use a fallback model for production workflows where primary model failures should not stop execution.

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
<tr><td style="word-wrap: break-word;">user_prompt</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The task, question, or instructions for the agent. Supports dynamic text and connected upstream values through variable substitution.</td><td style="word-wrap: break-word;">Review the attached customer onboarding notes and draft a concise follow-up email. Use {{CRM Lead.output}} for the lead details.</td></tr>
<tr><td style="word-wrap: break-word;">model</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Primary LLM model used for the agent loop. Available options are loaded from the configured model catalog and may vary by workspace.</td><td style="word-wrap: break-word;">gpt-4.1</td></tr>
<tr><td style="word-wrap: break-word;">max_tokens</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of tokens the model may generate in a response. Use 0 to omit the explicit limit. Range: 0 to 128000.</td><td style="word-wrap: break-word;">4096</td></tr>
<tr><td style="word-wrap: break-word;">system_prompt</td><td>False</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Role, behavior, tone, and boundaries for the agent before it handles the user request. If empty, a default helpful assistant prompt is used.</td><td style="word-wrap: break-word;">You are a careful customer support analyst. Use tools only when needed, cite knowledge base facts, and return concise recommendations.</td></tr>
<tr><td style="word-wrap: break-word;">context_data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Connected upstream workflow data available for prompt variable substitution. Supports multiple connections and can carry text, JSON, or other node outputs.</td><td style="word-wrap: break-word;">{"CRM Lead.output":"Acme Corp, renewal due July 15","Ticket Summary.response":"Customer reported delayed invoice sync."}</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Controls randomness. Lower values are more deterministic; higher values are more creative. Range: 0 to 2.</td><td style="word-wrap: break-word;">0.4</td></tr>
<tr><td style="word-wrap: break-word;">top_p</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Nucleus sampling parameter that limits token choices to a probability mass. Not supported by all providers. Range: 0 to 1.</td><td style="word-wrap: break-word;">0.9</td></tr>
<tr><td style="word-wrap: break-word;">top_k</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Limits token choices to the top K candidates. Not supported by all providers. Range: 1 to 500.</td><td style="word-wrap: break-word;">40</td></tr>
<tr><td style="word-wrap: break-word;">frequency_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Penalizes repeated wording to reduce repetitive phrasing. Availability depends on the selected provider. Range: -2 to 2.</td><td style="word-wrap: break-word;">0.3</td></tr>
<tr><td style="word-wrap: break-word;">presence_penalty</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Encourages the model to introduce new topics instead of repeating existing ones. Availability depends on the selected provider. Range: -2 to 2.</td><td style="word-wrap: break-word;">0.2</td></tr>
<tr><td style="word-wrap: break-word;">max_iterations</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of LLM/tool-calling rounds before stopping. Higher values allow more complex tasks but increase latency and cost. Range: 1 to 50.</td><td style="word-wrap: break-word;">8</td></tr>
<tr><td style="word-wrap: break-word;">memory_window_size</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of previous user/assistant message pairs to remember across executions for this workflow and node. 0 disables memory. Range: 0 to 100.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">enable_guardrails</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When enabled, detects and redacts common PII patterns such as email addresses, phone numbers, SSNs, and credit card numbers in inputs and outputs.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">json_mode</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Requests a pure valid JSON final response. Works best when paired with `response_schema` and downstream JSON parsing.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">response_schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON schema describing the desired shape of the final response when JSON Output is enabled. Invalid schema text is still included as format guidance.</td><td style="word-wrap: break-word;">{"type":"object","properties":{"summary":{"type":"string"},"priority":{"type":"string","enum":["low","medium","high"]},"next_steps":{"type":"array","items":{"type":"string"}}},"required":["summary","priority","next_steps"]}</td></tr>
<tr><td style="word-wrap: break-word;">max_kb_context_chars</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of knowledge base result characters injected into the prompt. Larger values provide more context but consume more model context. Range: 1000 to 500000.</td><td style="word-wrap: break-word;">50000</td></tr>
<tr><td style="word-wrap: break-word;">fallback_model</td><td>False</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Backup model to try automatically if the primary model call fails. Available options are loaded from the model catalog.</td><td style="word-wrap: break-word;">claude-opus-4-6</td></tr>
<tr><td style="word-wrap: break-word;">tools_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Tool configuration JSON managed by the Agent Tools panel. Defines connector tools or custom node tools the agent may call.</td><td style="word-wrap: break-word;">[{"type":"tool","tool_id":"send_slack_message","name":"Send Slack Message","credential_id":"slack-workspace-connection","input_schema":{"agent_decided_inputs":["channel","text"]}}]</td></tr>
<tr><td style="word-wrap: break-word;">knowledge_bases_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Knowledge base configuration JSON managed by the Knowledge Bases panel. Only configured knowledge base IDs can be searched by the agent.</td><td style="word-wrap: break-word;">[{"kb_id":"kb-support-runbooks","name":"Support Runbooks"},{"kb_id":"kb-product-docs","name":"Product Documentation"}]</td></tr>
<tr><td style="word-wrap: break-word;">encrypted_credentials_json</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Encrypted credential map injected by the platform for tool execution. This is platform-managed and should not be edited manually.</td><td style="word-wrap: break-word;">{"slack-workspace-connection":"<encrypted-credential-blob>"}</td></tr>
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
<tr><td style="word-wrap: break-word;">response</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Final answer from the agent, or the latest assistant text if the agent stops before completion. In JSON Output mode, this is coerced to valid JSON when possible.</td><td style="word-wrap: break-word;">{"summary":"The customer is blocked by a delayed invoice sync.","priority":"high","next_steps":["Open a billing engineering ticket","Notify the account owner","Send the customer a status update"]}</td></tr>
<tr><td style="word-wrap: break-word;">tool_calls_log</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted audit log of tool calls made by the agent, including iteration number, tool name, arguments, and result. Useful for debugging and traceability.</td><td style="word-wrap: break-word;">[{"iteration":1,"reasoning":"I need to check the runbook before recommending next steps.","tool_call_id":"call_7x2","tool_id":"search_knowledge_base","arguments":"{\"query\":\"invoice sync delay remediation\",\"knowledge_base_id\":\"kb-support-runbooks\"}","result":"{\"results\":[{\"title\":\"Invoice Sync Runbook\",\"content\":\"Check sync queue status and retry failed jobs.\"}],\"result_count\":1}"}]</td></tr>
<tr><td style="word-wrap: break-word;">reasoning_history</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted per-iteration reasoning trace showing whether the agent called tools or produced a final answer.</td><td style="word-wrap: break-word;">[{"iteration":1,"reasoning":"I should search the support runbook for invoice sync guidance.","action":"tool_call","tools_called":["search_knowledge_base"],"model":"gpt-4.1"},{"iteration":2,"reasoning":"I found the relevant runbook and can now answer.","action":"final_answer","tools_called":[],"model":"gpt-4.1"}]</td></tr>
<tr><td style="word-wrap: break-word;">is_complete</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the agent reached a final response before hitting `max_iterations`; false when it stopped early due to an error or iteration limit.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">conversation_history</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted message history after execution. Feed this back into `conversation_history` for loop-based workflows or retain it for inspection.</td><td style="word-wrap: break-word;">[{"role":"system","content":"You are a support triage assistant."},{"role":"user","content":"Triage ticket TCK-1842."},{"role":"assistant","content":"{\"summary\":\"Invoice sync delay detected.\",\"priority\":\"high\"}"}]</td></tr>
<tr><td style="word-wrap: break-word;">error</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Error or warning text from the run. Empty when the agent completes without warnings; may contain model failures, iteration-limit messages, invalid configuration errors, or PII warnings.</td><td style="word-wrap: break-word;">Agent reached max iterations (10)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each iteration can make one LLM call plus one or more tool calls. Higher `max_iterations`, large knowledge base context, and slow external connectors increase latency and cost.
- **Tool security**: Connector tools are executed through the platform backend so credentials remain server-side. The executing user must be authorized to use the configured connection or credential.
- **Knowledge base access**: The agent can only search knowledge bases configured on the node, and searches require a valid platform user identity. Unauthorized, missing, or unknown knowledge bases return no results rather than exposing content.
- **JSON behavior**: JSON Output mode strongly instructs the model to return JSON and performs best-effort cleanup, but malformed model output may be wrapped or coerced rather than matching the schema perfectly.
- **Memory behavior**: Conversation memory is scoped to the workflow, node, and system prompt. Changing the system prompt starts a separate memory context, and setting `memory_window_size` to 0 disables memory.

## Troubleshooting
- **`Model not found` or no models appear**: The selected model is not available in the model catalog or the catalog service is unavailable. Refresh available models, choose a listed model, or verify model-provider configuration.
- **Agent stops with `Agent reached max iterations`**: The model continued calling tools without producing a final answer. Lower tool ambiguity, improve the system prompt, reduce available tools, or increase `max_iterations` if the task genuinely needs more steps.
- **Tool call returns an authorization or credential error**: The configured connection or credential may be missing, revoked, or not accessible to the executing user. Reconnect the app, select the correct credential, and confirm the user has permission to use it.
- **Knowledge base search returns empty results**: The knowledge base may not be configured on the node, the executing user may lack access, the query may be too vague, or the KB may have no matching indexed content. Verify the selected KB and test a more specific query.
- **Downstream JSON parsing fails**: Enable `json_mode`, provide a strict `response_schema`, and make the prompt explicitly request only the fields your downstream workflow expects. Inspect `response` to confirm it is valid JSON before parsing.
