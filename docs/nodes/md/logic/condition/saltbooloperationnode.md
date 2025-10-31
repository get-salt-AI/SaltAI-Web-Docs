# Bool Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic boolean logic on two boolean inputs. Supports AND, OR, XOR between a and b, and a unary NOT operation on a. Returns a single boolean result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltbooloperationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to combine or invert boolean signals in a logic flow. It is typically placed after nodes that output BOOLEAN values (e.g., comparisons or condition checks) to build compound conditions or toggle states.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean operand. Used in all operations and the only operand for NOT.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean operand. Used for AND, OR, XOR. Ignored when operation is NOT a.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">op</td><td>True</td><td style="word-wrap: break-word;">['a AND b', 'a OR b', 'a XOR b', 'NOT a']</td><td style="word-wrap: break-word;">Selects the boolean operation to apply. For NOT a, only input a is considered.</td><td style="word-wrap: break-word;">a AND b</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The result of the selected boolean operation.</td><td style="word-wrap: break-word;">False</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation scope**: 'NOT a' ignores input b entirely.
- **Type expectation**: Inputs must be BOOLEAN; invalid states or errors fall back to a result of False.
- **XOR behavior**: 'a XOR b' is true only when exactly one of a or b is true.

## Troubleshooting
- **Unexpected False result**: Ensure both inputs are valid booleans and the correct operation is selected. Errors or unknown operations default to False.
- **Result not changing with b**: If the operation is 'NOT a', input b is intentionally ignored.
- **Logic seems inverted**: Double-check whether OR/AND/XOR was selected correctly, and remember XOR differs from OR when both inputs are true.
