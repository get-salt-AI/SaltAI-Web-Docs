# Do While Loop

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a simple do-while style loop controller. It forwards one or more data values through each iteration, evaluates a boolean condition at the end of the iteration, and either continues looping (on true) or exits and returns the final values (on false). The node exposes separate 'open' inputs for the initial iteration and 'close' inputs for subsequent iterations to simplify loop wiring.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltifnodesimpleloop.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to build iterative workflows where a condition determines whether to run another pass. Connect initial data into the open_value_N inputs, process inside the loop, compute a boolean condition at the end of each iteration, and feed updated values back via close_value_N. The node emits paired outputs for each value; the engine uses the condition to route the 'true' path back into the loop body and the 'false' path out of the loop.

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
<tr><td style="word-wrap: break-word;">open_value_1 ... open_value_N</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Initial iteration inputs. Provide any number of values (up to 100) to be processed within the loop on the first pass. Additional inputs appear progressively as you connect the previous one.</td><td style="word-wrap: break-word;">open_value_1 = 0, open_value_2 = "seed"</td></tr>
<tr><td style="word-wrap: break-word;">close_value_1 ... close_value_N</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Feedback inputs for subsequent iterations. After the first pass, provide the updated values here to continue iterating. If a close_value_N is not provided, the node falls back to the corresponding open_value_N.</td><td style="word-wrap: break-word;">close_value_1 = 1, close_value_2 = "updated"</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Loop continuation flag evaluated at the end of each iteration. Provide true to continue looping or false to exit. If omitted, the initial iteration defaults to true.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true, value_1_false, value_2_true, value_2_false, ...</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Interleaved outputs for each input value. The engine routes the 'true' outputs back into the loop body when condition is true, and the 'false' outputs forward to downstream nodes when condition is false. The number of output pairs scales with the number of connected value inputs.</td><td style="word-wrap: break-word;">value_1_true = 3, value_1_false = 3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Engine requirement**: This node requires the Salt Engine's dynamic I/O features.
- **Do-while behavior**: The initial iteration proceeds even if the condition is not provided; the condition is evaluated after the first pass.
- **Input pairing**: For each N, close_value_N overrides open_value_N on subsequent iterations. If close_value_N is absent, the node uses open_value_N.
- **Dynamic scaling**: Supports up to 100 value slots. Additional inputs appear as you connect the previous one.
- **Boolean handling**: Non-boolean condition inputs are coerced to boolean; strings like "true"/"false" are interpreted case-insensitively.
- **Output routing**: Both true and false outputs return the same values; the execution engine decides which path is active based on the condition.
- **Loop controller**: Marked as a loop controller; connect the 'true' path back into the loop body and the 'false' path to downstream consumers to exit.

## Troubleshooting
- **No iteration occurs**: Ensure the 'true' outputs are correctly connected back into the loop body and the condition evaluates to true when continuation is desired.
- **Infinite loop**: Verify your condition eventually becomes false, or add safeguards (e.g., a counter) to break the loop.
- **Missing values inside loop**: Connect either open_value_N (for the initial pass) or close_value_N (for subsequent passes). If neither is connected, the value propagates as null/None.
- **Unexpected early exit**: Confirm the condition uses 'true' to continue and 'false' to exit; invert your comparison if needed.
- **Too many/too few outputs visible**: The number of output pairs depends on how many value slots are connected. Connect additional value inputs to expose more output pairs.
- **Type mismatch errors downstream**: Each value slot is type-agnostic (*). Ensure downstream nodes accept the types you pass through the loop.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/logic/saltifnodesimpleloop/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

