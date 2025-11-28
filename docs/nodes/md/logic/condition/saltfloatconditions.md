# Float Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a comparison between two floating-point numbers and returns a boolean result. Supports standard comparison operators including equality, inequality, and relational checks. On invalid operations or runtime errors, it safely returns False.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltfloatconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to branch or gate logic based on comparisons between two numeric (float) values. Typical workflows include threshold checks (e.g., score > 0.7), bounds validation (min <= value <= max using two nodes), or feature flags tied to numeric inputs. Chain its boolean output into downstream logic nodes (e.g., If/Bool Operation) to control flow.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Left-hand operand for the comparison. Accepts floating-point values within the permitted range.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Right-hand operand for the comparison. Accepts floating-point values within the permitted range.</td><td style="word-wrap: break-word;">0.7</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Comparison operator to apply between a and b. Supported: '==', '!=', '<', '>', '<=', '>='.</td><td style="word-wrap: break-word;">></td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean result of the comparison between a and b using the selected operation.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Float equality caveat**: Comparing floats with '==' can be sensitive to precision. Consider using '<='/'>=' or pre-rounding inputs if you expect small numeric noise.
- **Input ranges**: a and b accept values roughly in [-9.99999999999e11, 9.99999999999e11]. Values outside this range are not allowed.
- **Step setting**: Inputs use a step of 1 in the UI; you can still enter decimal values directly.
- **Error handling**: If an unknown operation is provided or an error occurs, the node returns False.
- **NaN behavior**: Standard float rules apply; for example, NaN == any value is False, and NaN != any value is True.

## Troubleshooting
- **Unexpected False for near-equal numbers**: Floating-point precision may be the cause. Try rounding inputs before comparison or use '<='/'>=' instead of '=='.
- **Result always False**: Verify the 'operation' value is one of the supported options and that a and b are valid floats.
- **Cannot set certain values**: Ensure a and b are within the allowed numeric range.
- **Intermittent errors with special values**: Check for NaN or infinite values in inputs; sanitize or clamp inputs before passing them into the node.
