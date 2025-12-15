# Simple If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a boolean condition and conditionally routes one or more input values to paired True/False outputs. The node supports truly dynamic inputs and outputs: each connected value_N input generates two outputs (value_N_true and value_N_false). Actual branch selection is handled by the Salt engine at runtime; this node emits both branches while storing the condition for downstream routing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch execution based on a condition. Connect one or more value_N inputs you want to route. For each connected value, wire value_N_true to the nodes that should run when the condition is True and value_N_false to the nodes that should run when the condition is False. The engine will execute only the branch that matches the evaluated condition. This node is also suitable for loop patterns by feeding outputs back to earlier steps.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean condition that controls routing. True selects the True branch; False selects the False branch. Non-boolean values are coerced to boolean.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">value_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First value to route based on condition. Connecting this reveals value_2. Up to 100 values are supported.</td><td style="word-wrap: break-word;">Any data type (text, number, object, image, etc.)</td></tr>
<tr><td style="word-wrap: break-word;">value_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second value to route based on condition. Appears after value_1 is connected.</td><td style="word-wrap: break-word;">Any data type</td></tr>
<tr><td style="word-wrap: break-word;">value_3 ... value_100</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional routed values. Inputs appear progressively as the previous value_N is connected.</td><td style="word-wrap: break-word;">Any data type</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Routed output for value_1 when condition is True. Only the matching branch is executed downstream.</td><td style="word-wrap: break-word;">Same type as value_1</td></tr>
<tr><td style="word-wrap: break-word;">value_1_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Routed output for value_1 when condition is False. Only the matching branch is executed downstream.</td><td style="word-wrap: break-word;">Same type as value_1</td></tr>
<tr><td style="word-wrap: break-word;">value_2_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Routed output for value_2 when condition is True.</td><td style="word-wrap: break-word;">Same type as value_2</td></tr>
<tr><td style="word-wrap: break-word;">value_2_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Routed output for value_2 when condition is False.</td><td style="word-wrap: break-word;">Same type as value_2</td></tr>
<tr><td style="word-wrap: break-word;">value_N_true / value_N_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">For each connected input value_N, two outputs are created: value_N_true and value_N_false.</td><td style="word-wrap: break-word;">Same type as corresponding value_N</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Engine requirement**: This node requires the Salt engine's dynamic I/O support.
- **Dynamic I/O**: Outputs are generated only for connected value_N inputs. Each input creates two outputs (True/False).
- **Routing behavior**: The node returns both True and False outputs; the engine uses the stored condition to execute only the matching branch.
- **Condition coercion**: Non-boolean condition values (including strings like "true"/"false") are coerced to boolean; unexpected types may log warnings.
- **Scalability**: Supports up to 100 routed inputs (value_1 to value_100).
- **Branch merging**: To join branches after conditional execution, use the Merge If Branches node (SaltConditionalMerge).

## Troubleshooting
- **Outputs not visible**: Connect value_1 (and subsequent values) to reveal the corresponding True/False outputs. Outputs only appear for connected inputs.
- **Wrong branch runs or no execution**: Verify the condition input resolves to the expected boolean at runtime. Check upstream nodes and ensure the condition is wired correctly.
- **Unexpected data type warnings**: If you see logs about non-boolean condition values, ensure the condition input provides a BOOLEAN, or provide a clean True/False value.
- **Downstream node still runs on the other branch**: Confirm your downstream connections are wired to the correct value_N_true or value_N_false outputs. The engine will skip the non-matching branch based on the stored condition.
- **Need to rejoin branches**: Insert a Merge If Branches node after the conditional paths and wire each branch into a different input on the merge node.
