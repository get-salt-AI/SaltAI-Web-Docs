# Statistical Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Computes a single statistical measure from a comma-separated list of numeric values. Supports mean, median, mode, standard deviation, variance, sum, product, and count. The node parses the input string into numbers and returns the requested statistic as a float, applying population formulas for variance and standard deviation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltstatisticaloperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to derive a summary statistic from a list of values for downstream logic or parameterization. Typical workflows include aggregating scores, normalizing decisions based on data distribution, or counting items from a numeric list provided by earlier nodes.

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
<tr><td style="word-wrap: break-word;">values</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of numbers to analyze. Spaces are allowed and ignored around numbers. Non-numeric entries will cause the node to return 0.0.</td><td style="word-wrap: break-word;">1, 2, 3, 4, 5</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The statistical operation to compute. One of: mean, median, mode, std, variance, sum, product, count.</td><td style="word-wrap: break-word;">mean</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed statistic from the input list. Uses population formulas for variance and standard deviation; mode returns the most frequent value.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: Provide values as a comma-separated STRING (e.g., "1,2,3"). Any non-numeric token results in a 0.0 output.
- **Empty or invalid input**: If parsing yields no valid numbers, the node returns 0.0.
- **Population metrics**: std and variance are computed using population formulas (divide by N), not sample (N-1).
- **Mode ties**: If multiple values are equally frequent, the first most-common value encountered is returned.
- **Output type**: The result is returned as a FLOAT, even for operations like count.
- **Product initialization**: Product starts at 1 and multiplies across all numbers.

## Troubleshooting
- **Got 0.0 unexpectedly**: Ensure all entries in 'values' are valid numbers separated by commas; remove stray characters or empty entries.
- **Incorrect std/variance expectation**: This node uses population formulas. If you expected sample statistics, adjust accordingly (e.g., compute variance * N/(N-1)).
- **Mode seems ambiguous**: With multiple modes, the node returns the first most frequent value; pre-filter or deduplicate if a specific tie-breaking is required.
- **Precision issues**: Floating-point rounding can affect mean, std, and variance; limit value magnitudes or round downstream if necessary.
