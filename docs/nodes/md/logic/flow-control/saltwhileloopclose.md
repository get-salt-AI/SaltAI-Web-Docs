# While Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltWhileLoopClose is the deprecated close node for a simple while-style loop used with SaltWhileLoopOpen. It inspects a boolean condition to decide whether to stop the loop and return the current state values, or to dynamically clone and re-run all nodes contained between the loop’s open and close markers. It discovers loop-internal nodes from the graph structure, recreates them in a temporary subgraph, and forwards updated values back into the loop.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltWhileLoopClose only in existing or legacy workflows that already rely on the older While Loop Open/Close pair; for new designs, prefer SaltLoopOpen and SaltLoopClose. Place SaltWhileLoopOpen at the start of the section you want to repeat and SaltWhileLoopClose at the end. Connect the FLOW_CONTROL output from SaltWhileLoopOpen to the flow_control input of SaltWhileLoopClose. Drive the condition input from nodes that compute whether another iteration is required, such as a counter, a convergence check, or an external signal. The initial_valueN inputs are loop-carried state values—feed the corresponding outputs of your loop body back into these inputs so the updated state is used in the next iteration. A typical pattern is: SaltWhileLoopOpen → processing nodes (your loop body) → SaltWhileLoopClose; if condition is false, SaltWhileLoopClose simply returns the current values as final outputs; if true, it dynamically builds a subgraph of the contained nodes and schedules another iteration. It is also indirectly used by SaltForLoopOpen and SaltForLoopClose which wrap this while-loop mechanism to implement fixed-count loops. Always ensure your condition will eventually become false and that the state it depends on is actually updated inside the loop body, otherwise you risk non-terminating loops.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Control handle produced by SaltWhileLoopOpen (or via SaltForLoopOpen) that identifies this loop instance. It must be passed as a raw link from the matching While Loop Open node. The node uses this to locate the open node and determine which nodes are contained in the loop.</td><td style="word-wrap: break-word;">The FLOW_CONTROL output from a SaltWhileLoopOpen node configured with initial_value0=10 (remaining iterations) and initial_value1 as a latent image tensor.</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean flag that determines whether to continue looping. If false, the loop is considered finished and the node returns the current initial_valueN inputs as final outputs. If true, the node expands a new iteration by cloning the loop body nodes. This input is forced to come from another node (no fixed literal).</td><td style="word-wrap: break-word;">A boolean output from a comparison node that evaluates whether an integer counter is greater than 0.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First loop-carried value. Often used as an iteration counter or simple control variable, but it can be any data type. On each iteration this value is fed into the cloned SaltWhileLoopOpen node; when the loop terminates it will be returned as value0.</td><td style="word-wrap: break-word;">An integer 5 representing the number of remaining iterations.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second loop-carried value, typically used for the primary data being processed in the loop, such as an image, latent, or text. To persist updates across iterations, connect the loop body’s corresponding output back into this input.</td><td style="word-wrap: break-word;">A latent image tensor that is gradually denoised on each iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third loop-carried value. Use this when the loop body operates on multiple pieces of state in parallel—this slot can hold any additional data you want to update each iteration.</td><td style="word-wrap: break-word;">A segmentation mask tensor refined by a model each iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth loop-carried value. Works identically to the other initial_value slots and can carry arbitrary auxiliary data related to the loop.</td><td style="word-wrap: break-word;">A dictionary tracking per-iteration metrics such as loss history and best score.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth loop-carried value. All five initial_value inputs are symmetric; together they allow you to move multiple independent values through the same loop body.</td><td style="word-wrap: break-word;">A list of textual prompts or configuration variants accumulated across iterations.</td></tr>
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
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value0. When the loop finishes or an error occurs, this is simply the last value from initial_value0. When another iteration is scheduled, this is the updated value that will be passed back into initial_value0 on the next iteration.</td><td style="word-wrap: break-word;">An integer counter that has reached 0 after being decremented each loop iteration.</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value1, usually the main result of the loop body (for example, a final image or text after iterative refinement).</td><td style="word-wrap: break-word;">A final denoised image tensor after N iterative refinement steps.</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value2. This exposes any additional state that was carried and updated across the loop.</td><td style="word-wrap: break-word;">A binary mask that has stabilized after repeated refinement.</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value3, commonly used for auxiliary data or metrics that summarize the loop run.</td><td style="word-wrap: break-word;">A dictionary containing arrays of loss values for each iteration.</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value4. Use this for extra auxiliary results or accumulated data from the loop.</td><td style="word-wrap: break-word;">A list of all prompts used during the loop, in iteration order.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: For each additional iteration, the node dynamically clones all nodes that lie between the While Loop Open and While Loop Close, which can be expensive if the loop body is large or complex; keep your loop body as compact as possible.
- **Limitations**: This node is explicitly marked as deprecated in favor of SaltLoopOpen and SaltLoopClose, which provide clearer loop metadata and more robust behavior; new workflows should avoid relying on the deprecated While Loop nodes.
- **Behavior**: When condition evaluates to false, the node does not perform any graph expansion and simply returns the current initial_valueN inputs as final outputs, effectively treating the current state as the completed loop result.
- **Behavior**: Nodes are considered inside the loop based on connectivity between the While Loop Open and While Loop Close nodes; if you reuse intermediate nodes for both loop and non-loop branches, those nodes may be unexpectedly cloned and re-run as part of the loop.
- **Error Handling**: If an exception occurs while exploring dependencies or building the next-iteration subgraph, the node logs the error and returns the current initial_valueN values without further expansion, which can silently stop the looping behavior.

## Troubleshooting
- **Loop never terminates**: Symptom: execution appears to run indefinitely. Check that the condition input is wired correctly and can become false, and verify that the loop body updates any state (for example, a counter in initial_value0) that the condition depends on.
- **Unexpected nodes re-run each iteration**: Symptom: nodes that should be outside the loop are executed on every iteration. Inspect the wiring around SaltWhileLoopOpen and SaltWhileLoopClose and ensure only the intended nodes lie on paths between them; avoid sharing loop-internal nodes with unrelated branches.
- **Loop outputs do not change**: Symptom: value0–value4 remain constant across iterations. Confirm that the outputs produced by nodes in the loop body are wired back into the corresponding initial_valueN inputs on SaltWhileLoopClose so updated values are carried forward.
- **Graph or compatibility errors**: Symptom: errors mention graph building or inability to expand loops. This node depends on dynamic graph utilities from the engine; if your environment is outdated or missing required features, upgrade the engine and consider migrating to SaltLoopOpen and SaltLoopClose for a more robust loop implementation.
