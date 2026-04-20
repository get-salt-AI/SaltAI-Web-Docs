# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node lets you intentionally stop or block the execution of the current workflow based on a condition, while acting as a passthrough for the data flowing through it. It supports two halt modes: fully canceling the running workflow or blocking only the downstream branch that follows this node. You can optionally raise an alert when the halt is triggered to make the interruption explicit and easier to debug.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need conditional or manual stopping points in a workflow—for safety checks, guardrails, early exits, or debugging. Typically it sits mid-pipeline: connect any upstream data into the input field and wire its output forward to the nodes you want to gate behind a condition. For predictable scheduling, connect the output of a node that must run first into run_after so this node executes only after that upstream step.

Common scenarios:
- Early exit if a validation check fails (for example, bad user input, policy violation, or missing required data)
- Manually blocking a workflow branch while testing another branch
- Stopping long-running or resource-intensive branches based on a runtime flag
- Creating conditional flows when combined with logic nodes like IF Selector

Two halt methods are available: Cancel Prompt (method = False) interrupts the whole running workflow globally, while Block Execution (method = True) prevents subsequent nodes connected after this node from being executed while allowing the rest of the graph to continue. Upstream nodes commonly include logic or validation nodes that feed a boolean or condition into disable (directly or via an IF Selector). Downstream nodes are any expensive or sensitive operations you only want to run if execution is allowed, such as generation, external API calls, or data storage.

Best practices:
- Convert disable into an input and drive it from conditions to create reusable guardrail points.
- Use alert_on_trigger in development or when you want a hard stop and a clear error when execution is halted.
- Prefer Block Execution when you need fine-grained control of a specific branch; use Cancel Prompt to immediately stop the entire workflow.
- Use run_after purely for ordering constraints; do not rely on its value, as it is not forwarded downstream.

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Master switch that prevents halting when set to True. If this is True, the node will not halt or block execution and will simply pass the input value through. If False and there is a non-null input, the node may halt or block execution depending on method and alert_on_trigger.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Selects how execution is halted. When False (label: Cancel Prompt), the node will interrupt the entire running workflow when triggered. When True (label: Block Execution), the node will block only the downstream nodes that follow this one from running, using an execution blocker mechanism if available in the environment.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, an alert or exception is raised when the node halts or blocks execution, ensuring that no additional cached nodes continue and making the stop highly visible. If False, the node halts quietly: it still stops the flow, but does not raise an explicit exception.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Arbitrary data that flows through this node. When not halting, the node outputs exactly this value. When halting in Cancel Prompt mode, the output becomes None. When blocking execution, the output is a special blocker object that prevents subsequent nodes from running. This can be any supported Salt data type, such as text, images, models, or structured objects.</td><td style="word-wrap: break-word;">{"user_id": "user-123", "prompt": "Generate a product summary", "valid": false}</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Optional dependency-only input used to control execution order. Connect any upstream node whose completion should precede the evaluation of this node. The value itself is ignored; it is only used to ensure this node runs after the connected node.</td><td style="word-wrap: break-word;">Connect the output of a Validate Request node here to ensure validation runs before any potential halt.</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Primary passthrough output. If no halt condition is triggered (for example, disable is True or input is None), this returns the original input value unchanged, allowing execution to continue. If Cancel Prompt is used and triggered, the result is effectively None and execution is stopped. If Block Execution is used and supported, the result is a special blocker object that prevents downstream nodes connected after this node from being executed.</td><td style="word-wrap: break-word;">When continuing: the same object passed in as input, such as {"user_id": "user-123", "prompt": "Generate a product summary", "valid": true}. When blocking: a blocker object consumed by the execution engine to stop downstream nodes.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node itself is lightweight, but strategically placing it before expensive steps can significantly reduce resource usage by preventing unnecessary downstream computation.
- **Limitations**: The Block Execution method depends on the underlying execution engine. On older or incompatible installations, enabling Block Execution can raise an exception instructing you to update the environment.
- **Behavior**: Halting is only considered when disable is False and input is not None. If either condition is not met, the node always passes data through and reports that execution is continuing.
- **Behavior**: The run_after input is used solely to enforce ordering of node execution; its content is not forwarded or used in any logic.
- **Behavior**: With alert_on_trigger enabled in Cancel Prompt mode, the node raises an exception when halting, which will appear as an error and stop further execution immediately.
- **Limitations**: Rely on the actual data and blocker behavior to control your workflow; the UI text messages provided by this node are for human feedback and should not be parsed by downstream logic.

## Troubleshooting
- **Execution does not stop when expected**: Check that disable is set to False and that the input is not None at runtime. If disable is wired as an input, verify that your condition node outputs False when you intend to halt.
- **Error about being unable to block execution or needing an update**: This indicates the environment does not support the Block Execution method. Switch method to False (Cancel Prompt) or update your runtime environment to a version that supports execution blocking.
- **Downstream nodes still run after a supposed halt**: When using Cancel Prompt with alert_on_trigger set to False, some already-cached or scheduled nodes may appear to complete. Enable alert_on_trigger to enforce a hard stop and review your wiring to ensure all critical branches pass through this node.
- **Node never seems to execute**: Ensure the node is actually in the data path: it must receive a real value on input, and its output must be connected to the nodes you want to control. If execution order seems off, use run_after to guarantee this node runs after key upstream checks.
