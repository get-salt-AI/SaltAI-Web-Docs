# While Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node marks the beginning of a deprecated while-style loop block. It produces a FLOW_CONTROL token and up to five wildcard initial_value outputs that represent the state carried through each loop iteration. The boolean condition input is present but not enforced here; the actual loop continuation is controlled by the paired While Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use While Loop Open (Deprecated) at the start of a legacy looped section of your Salt workflow when you need to iterate over a block of nodes while carrying state between iterations. Connect its FLOW_CONTROL output directly to the flow_control input of SaltWhileLoopClose to form a loop region. The optional initial_value0–initial_value4 inputs seed up to five loop variables (of any data type supported by WILDCARD) that will circulate through the loop: wire these outputs to nodes inside the loop body, then route the updated values back into SaltWhileLoopClose's corresponding initial_value inputs. This node is part of the older flow-control system and is primarily maintained for backward compatibility; for new workflows, prefer SaltLoopOpen and SaltLoopClose, which offer a more flexible looping model. Typical upstream nodes include numeric constants (for counters), text or JSON nodes (for accumulators and metadata), or image/embedding sources, while typical downstream consumers are the processing nodes inside the loop and the SaltWhileLoopClose node that decides whether to continue looping.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Initial boolean flag associated with the loop. In this deprecated design, the real continuation decision is handled at SaltWhileLoopClose, so this value is best used for initial wiring consistency or as a placeholder input rather than the active loop gate.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First loop-carried value. May be any supported data type (number, text, image, list, dict, etc.). Commonly used as a counter, accumulator, or primary data item being iteratively transformed.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second loop-carried value. Use for additional state that persists across iterations, such as a growing text buffer, combined output, or auxiliary numeric metric.</td><td style="word-wrap: break-word;">""</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third loop-carried value for more complex loop state. Helpful when tracking multiple parallel variables within the same loop, such as an evolving list of results.</td><td style="word-wrap: break-word;">[]</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth loop-carried value. Optional, used when the loop body manages several related artifacts or parameters that all need to persist and update each iteration.</td><td style="word-wrap: break-word;">An image object representing the current frame being refined in each loop step</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth loop-carried value. The last available slot in this deprecated model; suitable for metadata, configuration, or any extra variable you want to propagate through the loop.</td><td style="word-wrap: break-word;">{"completed_steps": 0}</td></tr>
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
<tr><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Flow-control token that defines the loop region. Connect this directly to the flow_control input on SaltWhileLoopClose to create the loop. It is not meant for general data processing and is typically passed through unchanged.</td><td style="word-wrap: break-word;">A FLOW_CONTROL handle routed from While Loop Open to While Loop Close without branching.</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value0. This is the primary loop state value and is usually wired into the first input of nodes inside the loop body. It will be updated each iteration through SaltWhileLoopClose.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value1. Used for the second loop state value, often an evolving string, number, or structured object.</td><td style="word-wrap: break-word;">"step 0 completed"</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value2. Third loop state output, typically used for collections or secondary artifacts that grow or change each iteration.</td><td style="word-wrap: break-word;">[embedding_step_0]</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value3. Fourth loop state output for additional variables needed by the loop body.</td><td style="word-wrap: break-word;">An image tensor representing the current iteration's output</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Output corresponding to initial_value4. Fifth loop state output used for auxiliary metadata or configuration that evolves over iterations.</td><td style="word-wrap: break-word;">{"completed_steps": 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is inexpensive, but any nodes in the loop body will run once per iteration. Heavy models or I/O in the loop can greatly increase total runtime; consider limiting iterations or moving expensive steps outside the loop when possible.
- **Limitations**: This while-loop implementation is deprecated and limited to five loop-carried values. For newer and more flexible control-flow patterns, you should use SaltLoopOpen and SaltLoopClose instead.
- **Behavior**: The condition input on SaltWhileLoopOpen does not actually control when the loop stops; that logic is handled in SaltWhileLoopClose. Treat this node as the loop entry and state seeding point rather than the condition gate.
- **Behavior**: Any initial_value input left unconnected will default to null at runtime, and the corresponding output valueN will also be null. Ensure downstream nodes either handle missing values safely or that all required initial_value fields are explicitly set.

## Troubleshooting
- **Common Error 1**: Loop never stops or runs unexpectedly many times. Likely cause: the loop condition is wired only to SaltWhileLoopOpen instead of SaltWhileLoopClose. Solution: move or duplicate your condition wiring so the stopping logic is connected to the condition input on SaltWhileLoopClose.
- **Common Error 2**: Downstream node error about receiving a missing or invalid value where a specific type is expected. Likely cause: relying on an uninitialized initial_value input. Solution: provide explicit initial values for all loop-carried variables you use inside the loop, or add checks/defaults before consuming them.
- **Common Error 3**: Loop not recognized or no iteration occurs. Likely cause: FLOW_CONTROL output from While Loop Open is not connected directly to SaltWhileLoopClose or is split incorrectly. Solution: ensure there is a single, direct FLOW_CONTROL connection from SaltWhileLoopOpen to SaltWhileLoopClose without branching.
- **Common Error 4**: Difficulty modifying old workflows to use new loop nodes. Likely cause: mixing deprecated While Loop Open/Close with the newer Loop Open/Close. Solution: migrate the entire loop section to SaltLoopOpen and SaltLoopClose in one step, mapping each initial_value and value output to its new equivalent.
