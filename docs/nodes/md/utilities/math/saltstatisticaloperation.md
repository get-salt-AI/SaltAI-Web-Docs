# Statistical Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Computes a statistical metric from a comma-separated list of numbers. It parses the input string into floats and supports mean, median, mode, standard deviation, variance, sum, product, and count. If parsing fails or the list is empty, it returns 0.0 and logs a warning.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltstatisticaloperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly aggregate or summarize numeric data provided as a simple comma-separated string. Typical workflows include preprocessing text-based numeric inputs, computing summary statistics for parameters, or deriving a single scalar metric to drive downstream logic.

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
<tr><td style="word-wrap: break-word;">values</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of numeric values to process. Spaces around commas are allowed; numbers are parsed as floats.</td><td style="word-wrap: break-word;">1, 2, 3.5, 4, 10</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Statistical operation to apply to the parsed numbers. Options: mean, median, mode, std, variance, sum, product, count.</td><td style="word-wrap: break-word;">mean</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed statistical result as a float.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Note: Input must be a comma-separated list using '.' as the decimal separator. Non-numeric entries cause the node to return 0.0.
- Note: Standard deviation and variance are computed as population metrics (divide by N), not sample metrics (divide by N-1).
- Note: Mode returns the most frequent value; when multiple modes exist, the first encountered most frequent value is returned.
- Note: Median handles both even and odd counts; for even counts, it averages the two middle values.
- Note: Product initializes at 1 and multiplies through; very large or very long lists may underflow/overflow floating-point range.
- Note: If the parsed list is empty, the node returns 0.0.

## Troubleshooting
- Issue: Result is 0.0 unexpectedly. Resolution: Check that all entries in 'values' are valid numbers and that the list is not empty.
- Issue: Mode seems arbitrary with multiple equally frequent values. Resolution: The node returns the first most frequent value; if you need deterministic tie-breaking, pre-sort or adjust inputs.
- Issue: Unexpected precision or rounding differences. Resolution: The node uses floating-point arithmetic; consider rounding downstream if exact formatting is required.
- Issue: Very large product or sum returns inf or 0.0. Resolution: Scale inputs or use operations less sensitive to magnitude (e.g., mean) depending on your goal.
