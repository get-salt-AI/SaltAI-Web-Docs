# Simple If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltIfNode is a flexible conditional routing node that evaluates a boolean condition and passes each input value to either a true or false branch. Inputs and outputs are fully dynamic: value_1, value_2, and so on are created as you connect them, and corresponding outputs are generated to match. It is designed for branch control and is especially useful as the core building block for conditional flows and loop patterns.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltIfNode whenever you need to conditionally route one or more values based on a single boolean condition. Typical patterns include: (1) branching a workflow into "success" vs "fallback" paths, (2) gating downstream heavy computation behind a condition, (3) building loop constructs where data feeds back via the true or false branch, and (4) composing with "Merge If Branches" (SaltConditionalMerge) to create reusable if/else blocks.

In a typical workflow, upstream nodes provide the condition (for example a comparison node, an LLM decision node, or a boolean flag) and the data inputs (value_1, value_2, etc.). SaltIfNode sits at the fork point: you connect its true-side outputs (value_1_true, value_2_true, etc.) to the nodes that should run when the condition is true, and its false-side outputs (value_1_false, value_2_false, etc.) to the nodes that should run when the condition is false. Downstream, use SaltConditionalMerge to rejoin branches if you want to continue with a single unified flow.

Best practices: (1) Always provide a clean boolean to the condition input; if you must send strings, stick to "true" or "false" in lowercase to avoid surprises. (2) Only create as many value_X inputs as you actually need, to keep the workflow readable. (3) For loop-like structures, connect the "continue" path from value_X_true back into upstream processing, and route termination outputs to the rest of the workflow. (4) Combine multiple SaltIfNode nodes to encode more complex multi-step or nested condition logic when needed.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Single boolean that decides which branch is taken for every value input. True sends values to the true-side outputs, false sends them to the false-side outputs. Non-boolean values are coerced: the string "true" (case-insensitive) becomes true; any non-boolean is converted with a generic truthiness rule, which treats non-empty values as true.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First arbitrary value to route based on the condition. This can be any supported data type such as text, image, number, list, or structured object. When connected, SaltIfNode will expose value_2 as another available input. Only connected value_i inputs are considered at runtime.</td><td style="word-wrap: break-word;">High-level summary of the last customer support interaction</td></tr>
<tr><td style="word-wrap: break-word;">value_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second arbitrary value to route based on the same condition. Appears once value_1 is connected. You can continue adding up to 100 value_X inputs for parallel routing of multiple related pieces of data.</td><td style="word-wrap: break-word;">{"ticket_id": "SUP-4821", "priority": "high", "channel": "email"}</td></tr>
<tr><td style="word-wrap: break-word;">value_3..value_100</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional optional values to route. Each value_i input becomes visible only after value_(i-1) is connected, up to a maximum of 100 inputs. All are routed according to the same condition, making it easy to branch complex structured inputs together.</td><td style="word-wrap: break-word;">["embedding_vector_for_summary", "list_of_recommended_actions"]</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output carrying value_1 when the condition is true; otherwise null. Connect this to downstream nodes that should only run on the true branch. The data type exactly matches the type of value_1.</td><td style="word-wrap: break-word;">Escalation instruction text when condition = true; null when condition = false</td></tr>
<tr><td style="word-wrap: break-word;">value_1_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output carrying value_1 when the condition is false; otherwise null. Connect this to nodes that should process the alternative or fallback path.</td><td style="word-wrap: break-word;">Standard reply template when condition = false; null when condition = true</td></tr>
<tr><td style="word-wrap: break-word;">value_2_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output for value_2 on the true branch; null if the condition is false. Use this when you need to route multiple related values in sync, such as a payload and its metadata.</td><td style="word-wrap: break-word;">{"ticket_id": "SUP-4821", "status": "escalated"}</td></tr>
<tr><td style="word-wrap: break-word;">value_2_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output for value_2 on the false branch; null if the condition is true. Enables separate downstream flows for the same logical payload depending on which path is taken.</td><td style="word-wrap: break-word;">{"ticket_id": "SUP-4821", "status": "resolved_without_escalation"}</td></tr>
<tr><td style="word-wrap: break-word;">value_N_true/value_N_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional true/false outputs for each connected value_N input. Internally, the node returns outputs in an interleaved tuple pattern (v1_true, v1_false, v2_true, v2_false, ...), but the platform exposes them as named outputs. Only outputs corresponding to connected inputs are actually used.</td><td style="word-wrap: break-word;">For three inputs you would see value_3_true and value_3_false used for a third routed payload such as a generated PDF or an image.</td></tr>
<tr><td style="word-wrap: break-word;">condition (metadata)</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Metadata value recording the evaluated boolean condition for this execution. Used by the engine's conditional router to decide which downstream nodes receive data and are executed. This is not typically wired as a visible socket by users.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node supports up to 100 dynamic value inputs, but connecting many large payloads (for example, large images or big JSON objects) can increase memory usage and graph complexity; route only what is necessary on each branch.
- **Limitations**: All value_X inputs share a single condition. If you need different conditions per value, use multiple SaltIfNode instances or additional logic nodes before routing.
- **Behavior**: When a branch is not taken, the corresponding *_true or *_false outputs are explicitly set to null for that run. If you connect a downstream node directly to such an output without conditional execution, it may receive null and behave unexpectedly.
- **Behavior**: Condition inputs are coerced to boolean if not already a boolean. Strings equal to "true" (case-insensitive) become true; other non-empty values become true under generic truthiness rules, which can cause surprising behavior if you pass arbitrary text or objects.
- **Behavior**: Outputs are determined dynamically from the workflow wiring. If you add or remove value_X inputs, the exposed outputs will change, which may require you to re-check and adjust downstream connections.

## Troubleshooting
- **Condition always seems true or false**: If the node appears to always pick the same branch, inspect the upstream node providing `condition`. Passing non-empty strings such as "yes" or raw objects will be coerced to true. Ensure the upstream node emits a real boolean or an explicitly controlled string like "true" or "false".
- **Expected outputs not visible**: If you do not see `value_3_true` or `value_3_false`, verify that `value_3` is actually connected. The engine only creates outputs for connected inputs, so unconnected value_X entries will not expose corresponding outputs.
- **Downstream node receives null**: When a branch is not taken, that side's outputs are null. If a downstream node is wired to both branches without any conditional execution, it may receive null and fail or produce empty results. Guard it with conditional routing or use the "Merge If Branches" node to ensure only the executed branch's non-null value is forwarded.
- **Output ordering confusion in low-level inspection**: When inspecting raw engine responses, remember that outputs are returned as an interleaved tuple (v1_true, v1_false, v2_true, v2_false, ...). Always reference outputs by their names in the platform instead of relying on positional indices to avoid mapping values to the wrong branch.
