# Float Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a comparison between two floating-point numbers. Supports standard operations (==, !=, <, >, <=, >=) and returns a Boolean indicating the result. Designed for simple numeric checks to drive conditional logic in your workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltfloatconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two numeric values and branch logic based on the result. Typical scenarios include threshold checks (e.g., confidence > 0.8), range validation, and gating subsequent node execution on numeric conditions.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float value to compare. Accepts a wide range of values.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float value to compare against.</td><td style="word-wrap: break-word;">0.8</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comparison operator to apply between a and b. One of: '==', '!=', '<', '>', '<=', '>='.</td><td style="word-wrap: break-word;">>=</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the comparison evaluates to true, otherwise False.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Exact equality (==) on floats can be sensitive to precision; consider using <= or >= if appropriate for thresholds.
- Input ranges are large (-9.99999999999e11 to +9.99999999999e11). Values outside this range are not accepted.
- The step is configured as 1 for the UI control, but inputs are floating-point; you can still provide non-integer floats via connections or manual entry.
- On internal errors or unknown operations, the node returns False.

## Troubleshooting
- Unexpected False with '==' on floats: Floating-point precision may differ slightly. Use '<=' or '>=' around a tolerance if exact equality is unreliable.
- Type errors or invalid input: Ensure both a and b are valid numbers. Non-numeric or None inputs will cause the comparison to fail and return False.
- Wrong result direction: Double-check the selected operator (e.g., '<' vs '>') and confirm a and b are in the intended order.
