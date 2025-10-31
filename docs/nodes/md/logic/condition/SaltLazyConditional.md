# Lazy Conditional

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and returns the first value whose paired condition is true, evaluating inputs lazily. Only the first satisfied branch (or the else branch if none are true) is computed and pulled, saving time and resources. Supports up to 10 condition/value pairs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/SaltLazyConditional.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to route data based on multiple boolean conditions without computing every branch. Connect condition1/value1 through condition10/value10 in priority order. The node checks condition1 first, then condition2, and so on, returning the corresponding value for the first true condition. If no conditions are true, the else input is returned if provided.

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
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition1 is true. Evaluated only if condition1 is true.</td><td style="word-wrap: break-word;">An image, text, model, or any data type</td></tr>
<tr><td style="word-wrap: break-word;">condition1</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean condition (highest priority). Must be provided via a connection.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition2 is true and condition1 is false.</td><td style="word-wrap: break-word;">Alternative data to route</td></tr>
<tr><td style="word-wrap: break-word;">condition2</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean condition. Checked only if condition1 is false.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition3 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Another branch output</td></tr>
<tr><td style="word-wrap: break-word;">condition3</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Third boolean condition. Checked only if condition1 and condition2 are false.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition4 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 4 output</td></tr>
<tr><td style="word-wrap: break-word;">condition4</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fourth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition5 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 5 output</td></tr>
<tr><td style="word-wrap: break-word;">condition5</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fifth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition6 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 6 output</td></tr>
<tr><td style="word-wrap: break-word;">condition6</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Sixth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition7 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 7 output</td></tr>
<tr><td style="word-wrap: break-word;">condition7</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Seventh boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition8 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 8 output</td></tr>
<tr><td style="word-wrap: break-word;">condition8</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Eighth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition9 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 9 output</td></tr>
<tr><td style="word-wrap: break-word;">condition9</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Ninth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">value10</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition10 is true and all prior conditions are false.</td><td style="word-wrap: break-word;">Branch 10 output</td></tr>
<tr><td style="word-wrap: break-word;">condition10</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Tenth boolean condition.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">else</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fallback value returned if all conditions are false. Evaluated only when needed.</td><td style="word-wrap: break-word;">Default or safe output</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected output: the first value whose condition is true, or the else value if no conditions are true.</td><td style="word-wrap: break-word;">The routed data corresponding to the satisfied branch</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Order matters**: The node checks conditions from 1 to 10 and returns the first matching value.
- **Lazy evaluation**: Only the required branch (and its value) is computed/fetched based on the conditions.
- **Optional inputs**: All inputs are optional in the interface, but conditions must be connected for branches you intend to use.
- **Else fallback**: If no conditions are true and no else is provided, the output will be empty/null.
- **Type flexibility**: Values are WILDCARD; ensure the downstream node can handle the selected value's type.
- **Limit**: Supports up to 10 condition/value pairs.

## Troubleshooting
- **Node requests a specific condition or value input**: Connect the indicated conditionN or valueN. The node lazily asks only for what is needed to resolve the first true branch.
- **Unexpected null/empty output**: All conditions may be false and no else input was provided. Connect an else value or ensure at least one condition becomes true.
- **Wrong branch selected**: Verify the order and logic of your conditions; the node selects the first true condition in sequence.
- **Performance concerns**: Large or heavy value branches not selected are not evaluated. If you still see overhead, check upstream nodes for unnecessary pre-computation.
- **Downstream type mismatch**: Since outputs are WILDCARD, ensure the chosen branch produces a type compatible with the next node.
