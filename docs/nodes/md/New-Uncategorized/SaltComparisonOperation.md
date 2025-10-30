# Comparison Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs numeric comparisons and related utilities between two numbers. It can compute min/max, clamp a value to a range, or evaluate relational checks (equal, greater/less and their inclusive variants). Outputs both a numeric result and a boolean flag indicating the outcome for comparison operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltComparisonOperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two numeric values or constrain a value within bounds as part of a workflow. Typical patterns include branching logic based on greater/less checks, gating downstream operations using the boolean output, or normalizing values via clamping before further processing.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First numeric input to compare or clamp.</td><td style="word-wrap: break-word;">3.5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second numeric input used for comparisons or as the counterpart for min/max operations.</td><td style="word-wrap: break-word;">5.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform. One of: min, max, clamp, equal, greater, less, greater_equal, less_equal.</td><td style="word-wrap: break-word;">greater_equal</td></tr>
<tr><td style="word-wrap: break-word;">clamp_min</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Lower bound used when operation is clamp. Ignored for other operations.</td><td style="word-wrap: break-word;">0.0</td></tr>
<tr><td style="word-wrap: break-word;">clamp_max</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Upper bound used when operation is clamp. Ignored for other operations.</td><td style="word-wrap: break-word;">1.0</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Numeric result of the selected operation. For min/max: the min or max of a and b. For clamp: a constrained to [clamp_min, clamp_max]. For comparisons: returns a if the comparison is true, otherwise 0.0.</td><td style="word-wrap: break-word;">3.5</td></tr>
<tr><td style="word-wrap: break-word;">comparison_result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome for comparison operations: true if the relation holds, false otherwise. For min, max, and clamp, this is always false.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Min, max, and clamp always output comparison_result as false; use comparison operations if you need a boolean for branching.
- Equal uses a small tolerance for float comparison (approximately 1e-9), which helps avoid false negatives from floating-point precision.
- Clamp uses max(clamp_min, min(a, clamp_max)); ensure clamp_min is less than or equal to clamp_max for meaningful results.
- Input ranges are from approximately -1e10 to 1e10 with step granularity around 0.001.

## Troubleshooting
- Unexpected clamp result: Verify clamp_min <= clamp_max; reversed bounds can produce a value outside the intended range.
- Equality check failing due to precision: If values are nearly equal but comparison_result is false, consider numerical precision and that the tolerance is about 1e-9.
- comparison_result is always false: This is expected for min, max, and clamp. Use greater/less/equal variants if you need a boolean.
- result is 0.0 for a true comparison expected: For comparison operations, result returns a only when the comparison is true; if you expected b or another value, adjust downstream logic accordingly.
