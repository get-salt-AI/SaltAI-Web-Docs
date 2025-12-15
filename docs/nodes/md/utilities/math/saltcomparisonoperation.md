# Comparison Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs common numeric comparisons and range clamping between two float values. It returns a numeric result (e.g., min/max/clamped value or the left operand when a comparison is true) and a boolean indicating the outcome of comparison-type operations. Non-comparison operations set the boolean to false.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltcomparisonoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two numbers (e.g., thresholds, gating, branching) or constrain a value within bounds. Typical workflows include selecting the smaller/larger of two values, clamping a value to a range, or testing conditions like equal/greater/less to drive logic downstream via the boolean output.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First numeric value to compare or clamp. For comparison operations, this is the left-hand operand.</td><td style="word-wrap: break-word;">5.0</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second numeric value for comparison against 'a'. Ignored when operation is 'clamp'.</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Selects the operation to perform. Options: min, max, clamp, equal, greater, less, greater_equal, less_equal.</td><td style="word-wrap: break-word;">greater</td></tr>
<tr><td style="word-wrap: break-word;">clamp_min</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Lower bound used only when operation is 'clamp'.</td><td style="word-wrap: break-word;">0.0</td></tr>
<tr><td style="word-wrap: break-word;">clamp_max</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Upper bound used only when operation is 'clamp'.</td><td style="word-wrap: break-word;">1.0</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Numeric outcome. For min/max: the min or max of a and b. For clamp: 'a' clamped to [clamp_min, clamp_max]. For comparisons (equal/greater/less/greater_equal/less_equal): returns 'a' if the comparison is true, otherwise 0.0.</td><td style="word-wrap: break-word;">5.0</td></tr>
<tr><td style="word-wrap: break-word;">comparison_result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of comparison-type operations. True if the comparison condition is met. For non-comparison operations (min, max, clamp), this is always False.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For 'equal', floating-point equality uses a small tolerance (~1e-9) to account for precision errors.
- When operation is 'clamp', the 'b' input is ignored; only 'a', 'clamp_min', and 'clamp_max' are used.
- Ensure clamp_min is less than or equal to clamp_max to get meaningful clamping behavior.
- Non-comparison operations (min, max, clamp) always output comparison_result = False.
- All numeric inputs accept large ranges (approximately -1e10 to 1e10) and step granularity around 0.001.

## Troubleshooting
- Unexpected False on 'equal': Floating-point precision may prevent exact equality; adjust inputs or account for the 1e-9 tolerance.
- Clamp not behaving as expected: Verify clamp_min <= clamp_max and confirm that the selected operation is 'clamp'.
- comparison_result is always False: This is expected for 'min', 'max', and 'clamp'; use comparison operations to drive booleans.
- Result is 0.0 on a comparison: This means the condition evaluated to False; check 'a' and 'b' values and the chosen operator.
