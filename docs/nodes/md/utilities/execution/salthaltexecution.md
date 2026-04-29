# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltHaltExecution lets you intentionally stop a running workflow at a specific point. It behaves as a passthrough for any input data until it is triggered to halt, at which point it either cancels the entire run or blocks all downstream nodes connected after it. You can control when it halts via its own disable flag or by wiring it into conditional logic, and optionally emit an alert when a halt occurs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node anywhere in the middle of a workflow where you may need to abort further processing based on conditions such as safety checks, user opt-out, validation failures, or timeouts. Connect your ongoing data stream into the `input` so it can continue downstream when the node is not halting. Place SaltHaltExecution before expensive or sensitive operations, for example after a validation node and before a model invocation or external API call. Upstream, you typically connect data-producing nodes such as text generators, validators, or control-flow nodes (for example, SaltIfElse or comparison/boolean nodes) into `input` and/or into the disable control when exposed as an input. Downstream, any node that should only run if the workflow is allowed to continue can follow the output of this node. Use the `method` to decide how strict the halt is: as either a full prompt cancellation or a local block that prevents only subsequent nodes from running. The optional `run_after` input is useful when you must guarantee that this node executes after a particular upstream node; connect that node’s output to `run_after` purely to enforce ordering without affecting data. As a best practice, combine this node with a dedicated condition builder such as SaltIfElse, comparison nodes, or a policy-check node to keep your halt logic explicit and testable.

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Master switch for this node’s halting behavior. When set to True, the node will not halt execution even if it is reached; it simply passes data through. When False, the node is allowed to interrupt execution when its internal halt condition is met. You can convert this widget into an input to drive it from another node, such as an IF selector or comparison result.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Selects how the halt is applied. When False ("Cancel Prompt"), hitting this node will interrupt and cancel the entire workflow run. When True ("Block Execution"), only nodes that follow this node in the graph are prevented from running; already-completed nodes are unaffected. Use Cancel Prompt for hard-stops, for example serious policy violations, and Block Execution when you just want to skip remaining steps.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, the node raises an alert message when it interrupts execution. This is useful for surfacing why a run stopped early, for example to a UI log or notification system. If False, the workflow stops quietly without a special alert beyond the halt behavior itself.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary passthrough data for the workflow. Any type is accepted, including text, images, and structured objects. When the node does not halt, it returns exactly what it receives here. When the node halts, downstream nodes may not execute, so this value may not be consumed further.</td><td style="word-wrap: break-word;">A JSON object representing accumulated workflow state, such as {"user_query": "Summarize this article", "validated": true}</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional ordering-only input. Connect the output of any node you want this node to run after. The value passed here is not used or forwarded; it simply establishes an execution dependency so that SaltHaltExecution is evaluated after the connected node. This is useful when the halt decision must consider side-effects or results produced by another node.</td><td style="word-wrap: break-word;">Output of a safety-check node, ensuring SaltHaltExecution only runs after safety analysis is complete.</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Returns the same data that was received on the input field, unchanged. This is the continued data stream for downstream nodes, but it is only meaningful if execution is not halted or if the halt method is configured such that some downstream nodes can still run. Treat this as a transparent passthrough in normal flows.</td><td style="word-wrap: break-word;">The same state object passed into the input, for example {"user_query": "Summarize this article", "validated": true, "policy_ok": true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight; the main performance impact is that it can prevent heavier downstream nodes, such as large model calls, from running, which can significantly reduce resource usage when triggered early.
- **Limitations**: It does not retroactively undo side-effects of nodes that already ran, such as data writes or external API calls; it only stops future nodes from executing or cancels the remaining run.
- **Behavior**: The run_after input affects execution order but not data flow; its content is ignored and not forwarded to the output.
- **Behavior**: When disable is True, the node cannot halt execution under any condition and always acts purely as a passthrough, regardless of method or alert_on_trigger settings.

## Troubleshooting
- **Workflow continues when it should stop**: Check that disable is set to False or not being forced to True by an upstream condition node. Also verify that the node is actually on the path being executed and that your conditional logic is wired correctly.
- **Execution stops earlier or more broadly than expected**: Confirm the method setting. If it is set to Cancel Prompt, the entire run will halt, not just nodes after this point. Switch to Block Execution if you only want to skip downstream steps.
- **No alert appears when execution halts**: Ensure alert_on_trigger is set to True and that your environment surfaces alerts from this node, for example in run logs or notifications. If alerts are still missing, make sure the halt is actually being triggered, for example by inserting a simple test condition.
