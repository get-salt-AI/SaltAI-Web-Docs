# Bool Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic boolean logic between two boolean inputs. Supports AND, OR, XOR between a and b, and a unary NOT that inverts a. Returns a single boolean result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltbooloperationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine or invert boolean flags in a workflow, such as gating execution, selecting branches, or building compound conditions. Choose the desired operation via the op selector; for NOT, only the first input (a) is used.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean operand. Used by all operations and the sole input for NOT.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean operand. Ignored when op is NOT a.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">op</td><td>True</td><td style="word-wrap: break-word;">enum</td><td style="word-wrap: break-word;">The boolean operation to apply. Options: a AND b, a OR b, a XOR b, NOT a.</td><td style="word-wrap: break-word;">a AND b</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean outcome of the selected operation.</td><td style="word-wrap: break-word;">False</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For NOT a, the b input is ignored.
- XOR returns true only when exactly one of a or b is true.
- If an unknown operation is provided or an error occurs, the node returns false.
- Inputs must be boolean values; passing non-boolean types may lead to unintended results.

## Troubleshooting
- Result is always false: Verify the op value matches one of the allowed options and that both inputs are valid booleans.
- Unexpected use of b with NOT: Ensure you understand that NOT a ignores b.
- XOR behaving like OR: Remember XOR is exclusive; it returns true only when a and b differ.
- Type errors or coercion issues: Make sure upstream nodes output BOOLEAN, not strings like "true" or "false".
