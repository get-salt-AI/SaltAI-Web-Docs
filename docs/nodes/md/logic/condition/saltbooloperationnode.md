# Bool Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic boolean logic operations on two boolean inputs. Supports AND, OR, XOR, and a unary NOT on the first input. Returns a single boolean result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltbooloperationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to combine or invert boolean conditions within a logic flow. Typical usage includes gating branches, combining multiple comparisons, or toggling behavior based on multiple flags. Note that when selecting NOT a, the second input b is ignored.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean operand. Used for all operations and as the target for NOT.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean operand. Ignored when the operation is NOT a.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">op</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Boolean operation to apply. Choices: a AND b, a OR b, a XOR b, NOT a.</td><td style="word-wrap: break-word;">a XOR b</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The outcome of the selected boolean operation.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation scope**: For NOT a, input b is ignored.
- **XOR behavior**: a XOR b returns true only when exactly one of a or b is true.
- **Invalid operation handling**: If an unknown operation is provided, the node returns false.
- **Error handling**: On internal errors, the node returns false.

## Troubleshooting
- **Unexpected false result**: Verify the selected op value matches one of the allowed choices exactly (a AND b, a OR b, a XOR b, NOT a).
- **Result not changing when using NOT**: Remember that NOT a ignores b; ensure you intended to invert a and not b.
- **Type errors**: Ensure both a and b are valid booleans (true/false). Non-boolean inputs will lead to a false result.
- **Confusion with XOR**: If you expect true when both inputs are true, use a AND b instead of a XOR b.
