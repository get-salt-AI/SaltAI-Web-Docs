# Comparison Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs numeric comparisons and basic bound operations between two values. Supports min/max selection, clamping, and relational checks with a small float tolerance for equality. Returns both a numeric result and a boolean flag indicating the outcome for comparison modes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltcomparisonoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two numeric values, select a minimum/maximum, clamp a value within bounds, or branch logic based on relational conditions. It commonly precedes conditional routing, thresholding, or normalization steps where a numeric pass-through and a boolean decision are both useful.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First numeric input used for comparison or clamping.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second numeric input used for comparison against 'a'. Not used for clamp operation.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Select the operation to perform: min, max, clamp, equal, greater, less, greater_equal, less_equal.</td><td style="word-wrap: break-word;">greater_equal</td></tr>
<tr><td style="word-wrap: break-word;">clamp_min</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Lower bound for clamping when operation is 'clamp'. Ignored for other operations.</td><td style="word-wrap: break-word;">0.0</td></tr>
<tr><td style="word-wrap: break-word;">clamp_max</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Upper bound for clamping when operation is 'clamp'. Ignored for other operations.</td><td style="word-wrap: break-word;">1.0</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Numeric result of the operation. For min/max: the selected value. For clamp: the clamped 'a'. For comparisons: returns 'a' if the condition is true, else 0.0.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">comparison_result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the comparison. For min/max/clamp, this is always false; for relational operations it reflects whether the condition holds.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Equality tolerance**: 'equal' uses a small tolerance (approximately 1e-9) to account for floating-point precision.
- **Comparison outputs**: For 'equal', 'greater', 'less', 'greater_equal', and 'less_equal', the float result is 'a' when true, otherwise 0.0.
- **Non-comparison modes**: For 'min', 'max', and 'clamp', the boolean output is always false.
- **Clamp behavior**: Clamping is performed as max(clamp_min, min(a, clamp_max)). Ensure clamp_min <= clamp_max.
- **Input ranges**: Inputs are constrained internally to roughly [-1e10, 1e10] with a step of 0.001.
- **Error handling**: On invalid operation or processing errors, the node returns result=0.0 and comparison_result=false.

## Troubleshooting
- **Unexpected false on 'equal'**: Slight float differences may fail equality. Consider the tolerance (≈1e-9) or adjust upstream rounding.
- **Clamp not working as expected**: Verify that 'operation' is set to 'clamp' and that clamp_min <= clamp_max.
- **Zero result on true comparison expected**: Remember that for comparisons the numeric 'result' returns 'a' when true and 0.0 when false; check the boolean output for the actual condition.
- **Outputs always false**: Min/max/clamp operations always output false for the boolean field by design; switch to a relational operation if you need a boolean condition.
- **Out-of-range inputs**: Values far outside [-1e10, 1e10] may be clamped by input constraints; normalize inputs upstream if needed.
