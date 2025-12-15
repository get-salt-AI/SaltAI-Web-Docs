# Float Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a comparison between two floating-point numbers and returns a boolean result. Supports equality, inequality, and standard relational operators. If an unknown operation is selected or an error occurs during evaluation, the node returns False.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltfloatconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two float values and drive conditional logic based on the result. Typical use cases include gating execution paths, enabling/disabling features based on thresholds, or validating numeric conditions before proceeding in a workflow.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand for the comparison.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand for the comparison.</td><td style="word-wrap: break-word;">2.718</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (choice)</td><td style="word-wrap: break-word;">The comparison operator to apply between a and b. One of: ==, !=, <, >, <=, >=</td><td style="word-wrap: break-word;">></td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean outcome of the selected comparison between a and b.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Floating-point equality is exact**: The '==' operation uses exact comparison; consider precision issues when comparing computed floats.
- **Value range and step**: Inputs accept values from -999999999999.0 to 999999999999.0 with a step of 1.0.
- **Error handling**: Unknown operations or runtime errors cause the node to return False and log a warning.
- **NaN behavior**: If a or b is NaN, typical comparison semantics apply (e.g., a == b is False, a != b is True).

## Troubleshooting
- **Unexpected False with '=='**: Due to floating-point precision, exact equality may fail. Use '<=' and '>=' around a tolerance band or pre-round values.
- **Result always False**: Check that 'operation' is one of the supported options (==, !=, <, >, <=, >=).
- **Values out of range**: Ensure a and b are within the accepted range; out-of-range values may be clamped or rejected by the UI.
- **Comparisons with NaN**: If inputs may be NaN, prefer '!=' or validate/sanitize inputs before comparison.
