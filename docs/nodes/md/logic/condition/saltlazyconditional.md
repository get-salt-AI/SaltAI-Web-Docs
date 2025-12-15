# Lazy Conditional

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates up to 10 boolean conditions in order and returns the first corresponding value whose condition is true. It is lazy: only the value for the first true condition (or the else value) is computed/requested; all other branches are not evaluated.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyconditional.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to select among multiple alternative inputs based on boolean conditions without incurring the cost of computing all branches. Connect condition1/value1 for the highest-priority branch, followed by condition2/value2, and so on. If no conditions are true, the node outputs the 'else' input if provided.

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
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition1 is true. Evaluated lazily only when condition1 is true.</td><td style="word-wrap: break-word;">any supported type (e.g., text, image, number, dict)</td></tr>
<tr><td style="word-wrap: break-word;">condition1</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First condition tested. If true, value1 is selected.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition2 is true and condition1 was false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition2</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second condition tested, after condition1.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition3 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition3</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Third condition tested.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition4 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition4</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fourth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition5 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition5</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fifth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition6 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition6</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Sixth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition7 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition7</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Seventh condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition8 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition8</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Eighth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition9 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition9</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Ninth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">value10</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output if condition10 is true and all prior conditions were false.</td><td style="word-wrap: break-word;">any supported type</td></tr>
<tr><td style="word-wrap: break-word;">condition10</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Tenth condition tested.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">else</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fallback value if none of the conditions are true. Evaluated only if reached.</td><td style="word-wrap: break-word;">any supported type</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value from the first true condition, or the 'else' value if no conditions are true.</td><td style="word-wrap: break-word;">the chosen branch output (e.g., an image, text, number, or object)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Order matters**: Conditions are evaluated from condition1 to condition10; the first true condition wins.
- **Lazy evaluation**: Only the selected branch's value (or 'else') is computed. Unselected branches are not executed.
- **Up to 10 branches**: Supports at most 10 condition/value pairs.
- **Boolean inputs**: Each condition input must be provided for correct evaluation. The node may request missing conditions when needed.
- **Type consistency downstream**: The output type is WILDCARD; ensure downstream nodes can handle the type from whichever branch is selected.
- **Fallback behavior**: If no condition is true and 'else' is not connected, the output will be null/empty.

## Troubleshooting
- **No output produced**: If the result is null, ensure at least one condition is true or connect the 'else' input.
- **Unexpected branch selected**: Verify the boolean values and remember that evaluation stops at the first true condition.
- **Downstream type errors**: Align the types of all possible branch outputs to what downstream nodes expect.
- **Performance not improved**: Confirm heavy computations are placed in branch values and that only the intended condition triggers them.
- **Missing input requested**: If the node asks for a specific condition or value, connect that input; the node requests only what is required for the chosen path.
