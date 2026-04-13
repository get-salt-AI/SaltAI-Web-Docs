# Do While Loop

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a simple do-while style loop controller. It forwards one or more data values through each iteration, evaluates a boolean condition at the end of the iteration, and either continues looping on true or exits and returns the final values on false. Separate initial (open) and feedback (close) inputs are provided to simplify wiring iterative workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltifnodesimpleloop.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node for iterative workflows where at least one iteration must always run and a condition determines whether to continue. Connect initial values to open_value_N for the first pass, process them inside the loop, compute a boolean condition at the end of each iteration, and feed updated values back into close_value_N. Wire value_N_true outputs back into the loop body to continue looping, and route value_N_false outputs to nodes that should execute only after the loop has finished.

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
<tr><td style="word-wrap: break-word;">open_value_1 ... open_value_N</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Initial iteration inputs. Up to 100 values can be provided and are available inside the loop on the first pass. These are the starting values for the loop; additional numbered inputs appear progressively as you connect the previous one.</td><td style="word-wrap: break-word;">open_value_1 = 0, open_value_2 = "seed"</td></tr>
<tr><td style="word-wrap: break-word;">close_value_1 ... close_value_N</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Feedback inputs for subsequent iterations. After the first pass, provide updated values here to continue iterating. For each index N, close_value_N overrides open_value_N on subsequent iterations; if a close_value_N is not provided, the node reuses the corresponding open_value_N.</td><td style="word-wrap: break-word;">close_value_1 = 1, close_value_2 = "updated"</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Loop continuation flag evaluated at the end of each iteration. When true, the loop continues; when false, the loop exits. If omitted for the first run, the loop still performs the initial iteration and then evaluates the condition. Non-boolean inputs are coerced to boolean, and strings such as "true" and "false" are interpreted case-insensitively.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true, value_1_false, value_2_true, value_2_false, ...</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Interleaved output pairs for each input value. For every N, value_N_true and value_N_false both carry the same value; the execution engine uses the evaluated condition to decide which branch is active. True outputs are intended to be fed back into the loop body, while false outputs are intended for downstream processing after the loop terminates. The number of output pairs scales with the number of connected value inputs.</td><td style="word-wrap: break-word;">value_1_true = 3, value_1_false = 3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Engine requirement**: This node depends on the Salt Engine's dynamic input and output features and loop controller mechanics; ensure your environment supports these capabilities.
- **Do-while behavior**: At least one iteration is always executed. The condition is evaluated after the first pass, so the loop runs once even if no condition is supplied at the start.
- **Input pairing rules**: For each index N, close_value_N is used for all iterations after the first when connected. If it is not connected, open_value_N is reused. If neither open_value_N nor close_value_N is set, the corresponding value will be null or None.
- **Dynamic scaling**: Supports up to 100 value slots. New open_value_N and close_value_N fields are revealed only after connecting the previous ones, which keeps the interface manageable.
- **Boolean coercion**: Non-boolean condition inputs are converted to boolean. For example, non-empty strings or non-zero numbers become true. To avoid ambiguity, prefer explicit boolean outputs from comparison or logic nodes.
- **Output routing semantics**: True and false outputs carry identical data; their purpose is to define separate control-flow paths. The active path depends only on the evaluated condition.
- **Loop wiring pattern**: Treat this node as a loop controller: wire value_N_true outputs back into the loop body and value_N_false outputs to post-loop processing nodes, so iterative and final stages remain clearly separated.

## Troubleshooting
- **No iteration occurs**: Ensure that at least one of the true outputs is wired into nodes that eventually feed close_value_N, and verify that the condition evaluates to true when another iteration is expected.
- **Infinite loop**: Make sure the loop body updates data or counters so the condition can eventually become false. Add a maximum iteration counter and break condition if necessary.
- **Missing values inside loop**: Verify that for each index N, at least open_value_N is connected for the initial pass or close_value_N is wired for later passes; otherwise that slot will be null or None.
- **Unexpected early exit**: Check that the logic building the condition uses true to mean "continue" and false to mean "stop". If reversed, invert the condition or adjust comparison operators.
- **Too many or too few visible I/O slots**: The number of value_N_true and value_N_false outputs, as well as open and close inputs, is determined by how many value inputs are connected. Connect the next open_value_N to expose additional slots or disconnect unused inputs to simplify the interface.
- **Type mismatch in downstream nodes**: Each value slot is type-agnostic ("*"). If downstream nodes expect specific types such as images, numbers, or text, ensure you pass compatible values through the relevant value slots or insert conversion nodes inside the loop.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/logic/saltifnodesimpleloop/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

