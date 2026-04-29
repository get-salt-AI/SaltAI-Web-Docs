# While Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node works with While Loop Open (Deprecated) to implement a while-style loop region in your workflow. It receives a flow_control token, a boolean condition, and up to five loop-carried values, then either schedules another iteration or returns the final values when the condition is false. While functional, this node is deprecated in favor of the newer Loop Open / Loop Close pair and should mainly be used to maintain or extend existing flows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use While Loop Close (Deprecated) when you are maintaining or extending a legacy workflow that already uses While Loop Open (Deprecated). Place While Loop Open at the start of your looped region to emit the FLOW_CONTROL token and initial values, then route that FLOW_CONTROL output into the flow_control input of While Loop Close at the end of the loop body. Inside the loop body, wire the values coming from While Loop Open (or from a previous While Loop Close) into your processing nodes, then feed the updated values back into this node’s initial_value0–initial_value4 inputs. Drive the condition input from a boolean node that determines whether another iteration is needed (for example, a counter comparison or convergence check). When condition becomes false, the loop ends and this node outputs the final value0–value4 states for downstream consumers. In typical workflows, upstream you will use While Loop Open (Deprecated) or SaltForLoopClose, and downstream you will route value0–value4 into result collection, reporting, or further processing nodes. For new flows, prefer Loop Open / Loop Close instead of this deprecated pair.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Flow-control token that identifies the loop context. Must come from the FLOW_CONTROL output of SaltWhileLoopOpen (Deprecated) or from a compatible loop-constructing node. It is used internally to manage iterations and should always be connected directly, not synthesized manually.</td><td style="word-wrap: break-word;">FLOW_CONTROL output from a While Loop Open (Deprecated) node that begins the loop region.</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean flag that decides if the loop continues. When true, the loop is scheduled to run another iteration with the provided initial_value inputs as the new state. When false, the loop terminates and the current values are returned as final outputs. This is force-input only, so it must be wired from another node.</td><td style="word-wrap: break-word;">Output of a SaltIntConditions node evaluating current_step > 0 for a countdown loop.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First loop-carried value. On the first iteration, typically connected from While Loop Open’s value0; on later iterations, usually connected from this node’s own value0 output. Accepts any supported data type (numbers, text, images, lists, dicts, etc.).</td><td style="word-wrap: break-word;">An integer loop counter such as 10 that is decremented each iteration until it reaches 0.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second loop-carried state value. Used to carry additional information that evolves over iterations. Usually seeded from While Loop Open’s value1 and then fed from this node’s value1 output on subsequent passes.</td><td style="word-wrap: break-word;">A floating-point running sum of error values accumulated over multiple refinement steps.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third loop-carried value slot for arbitrary per-iteration state, such as auxiliary data or logs.</td><td style="word-wrap: break-word;">A text string that concatenates per-iteration status messages into a single log transcript.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth loop-carried value slot. Use this for additional state that must persist and be updated across loop iterations.</td><td style="word-wrap: break-word;">A list of processed item IDs that is extended with new IDs each iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth loop-carried value slot, allowing you to track up to five separate pieces of state through the loop body.</td><td style="word-wrap: break-word;">A dictionary of metrics such as {"loss": 0.23, "accuracy": 0.91} updated on each training iteration.</td></tr>
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
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried or final value corresponding to initial_value0. During looping, it is commonly fed back into initial_value0 for the next iteration. Once condition becomes false, this output holds the final state for downstream use.</td><td style="word-wrap: break-word;">0, representing a completed countdown that started from 10 and decremented to 0.</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried or final value corresponding to initial_value1. Intended for state that must be preserved after the loop finishes.</td><td style="word-wrap: break-word;">125.4, the total accumulated score after all refinement iterations are complete.</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried or final value corresponding to initial_value2, often used for secondary results or logs.</td><td style="word-wrap: break-word;">A long text report that merges summaries generated on each iteration into a single narrative.</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried or final value corresponding to initial_value3, often holding tertiary or diagnostic data.</td><td style="word-wrap: break-word;">A list of 20 processed image URLs gathered over the lifetime of the loop.</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried or final value corresponding to initial_value4. This is the last of the five available loop-carried outputs.</td><td style="word-wrap: break-word;">{"loss_history":[0.8,0.6,0.4],"accuracy_history":[0.5,0.7,0.85]} summarizing metric trajectories over iterations.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Loops can generate large execution graphs if the condition remains true for many iterations; design conservative stop conditions or add an explicit iteration cap to avoid excessive resource use.
- **Limitations**: This node is deprecated; for all new workflows you should use Loop Open and Loop Close, which offer a more modern and better-supported control-flow pattern.
- **Behavior**: The condition input has forceInput enabled and no default value, so leaving it unconnected will prevent the loop from behaving as intended and may cause errors or unexpected termination.
- **Behavior**: All initial_value inputs are wildcard-typed and passed through without validation; mismatched or changing data shapes across iterations can cause downstream nodes in the loop body to fail. Ensure the data type and structure are consistent each iteration.

## Troubleshooting
- **Loop never terminates or graph explodes in size**: Check that the condition input is actually updated within the loop and will eventually evaluate to false (for example, verify that your counter is being changed and that the comparison operation is correct).
- **Outputs are None or incorrect**: If value0–value4 are None or not what you expect, confirm that you are wiring the outputs from While Loop Open (or from a previous While Loop Close) into the loop body and then feeding the updated results back into the matching initial_value inputs on this node.
- **Cannot connect flow_control**: If there is no compatible FLOW_CONTROL output available, ensure your loop region begins with a While Loop Open (Deprecated) or a compatible loop node, and that you have not broken the flow-control chain by inserting incompatible nodes between the open and close.
- **Issues migrating legacy loops**: When modernizing a graph, mixing While Loop Open/Close with the newer Loop Open/Close in the same loop can cause wiring confusion. Migrate entire loop regions at once or keep legacy loops isolated to avoid incorrect connections.
