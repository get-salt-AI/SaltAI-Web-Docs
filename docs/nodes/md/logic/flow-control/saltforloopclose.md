# For Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Closes a deprecated for-loop construct. It decrements the loop counter and re-invokes the enclosed subgraph while the remaining count is greater than zero, passing through any auxiliary values each iteration. Use this only for legacy graphs; prefer the newer Loop Open/Close nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Pair this node with For Loop Open (Deprecated). Connect For Loop Open’s flow_control output to this node’s flow_control input. Provide any values you want to persist across iterations via the optional initial_value inputs. The node internally decrements the counter and continues looping until the remaining value is no longer greater than zero, then returns the final carried values.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop control link from For Loop Open (Deprecated). Drives the loop’s lifecycle.</td><td style="word-wrap: break-word;">Link from For Loop Open (Deprecated) -> flow_control</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First value to carry through and update each iteration.</td><td style="word-wrap: break-word;">Text, Image, Dict, or any data</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second value to carry through and update each iteration.</td><td style="word-wrap: break-word;">Counter, List, or any data</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third value to carry through and update each iteration.</td><td style="word-wrap: break-word;">Accumulated result object</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth value to carry through and update each iteration.</td><td style="word-wrap: break-word;">Auxiliary state</td></tr>
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
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the first carried data after the loop completes.</td><td style="word-wrap: break-word;">Final text/image/result object</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the second carried data after the loop completes.</td><td style="word-wrap: break-word;">Final counter/list/state</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the third carried data after the loop completes.</td><td style="word-wrap: break-word;">Final accumulated result</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the fourth carried data after the loop completes.</td><td style="word-wrap: break-word;">Final auxiliary state</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. Prefer using Loop Open and Loop Close for new workflows.
- Works only when connected to For Loop Open (Deprecated) via the flow_control input.
- The loop counter is decremented each iteration and the loop continues while remaining > 0.
- Optional inputs initial_value1–initial_value4 are carried across iterations and returned as outputs.
- On internal errors, outputs may return as None.
- Requires supporting arithmetic and comparison nodes (integer subtraction and condition). If those nodes are missing, the loop will fail to expand.

## Troubleshooting
- Loop never runs or exits immediately: Ensure the remaining value provided by For Loop Open is greater than 0.
- Graph doesn’t expand or errors about missing operations: Make sure integer math and condition nodes are available and correctly installed.
- Outputs are None: This typically indicates an internal error during expansion; check the node connections and types of initial_value inputs.
- No updates to carried values: Verify you’ve wired the outputs of the loop body back into the corresponding initial_value inputs on For Loop Close.
