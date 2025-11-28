# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs trigonometric and inverse trigonometric computations on a single numeric input. Supports sine, cosine, tangent, their inverses, and the hyperbolic counterparts. You can choose whether inputs are interpreted as degrees (for direct trig) and whether inverse results are returned in degrees.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to compute trigonometric values within a workflow, such as angle-to-ratio conversions, inverse angle retrieval, or hyperbolic functions for numeric processing. Set use_degrees to True to provide angles in degrees for sin/cos/tan and to receive inverse results in degrees; otherwise work in radians.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input for the trigonometric operation. Interpreted as degrees for sin/cos/tan when use_degrees is True; otherwise radians. For inverse functions (asin/acos), the valid input domain is [-1, 1].</td><td style="word-wrap: break-word;">45.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Which trigonometric operation to apply.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When True: sin/cos/tan expect degrees and inverse results are returned in degrees. When False: operate in radians.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed trigonometric result as a floating-point number. For inverse operations, the unit of the output depends on use_degrees.</td><td style="word-wrap: break-word;">0.70710678</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For sin, cos, tan: When use_degrees is True, input value is treated as degrees (e.g., 180 means π radians). When False, input is in radians.
- For asin and acos: The valid input domain is [-1, 1]. If the input is outside this range, the node returns 0.0 and logs a warning.
- For inverse functions (asin, acos, atan): When use_degrees is True, the output is returned in degrees; when False, the output is in radians.
- Tangent can grow very large near its asymptotes (e.g., ±90 degrees or ±π/2 radians). Expect large magnitudes in those regions.
- Input range is limited to [-360, 360] by the node configuration. Ensure your radian inputs fall within this numeric range if use_degrees is False.

## Troubleshooting
- Result is 0.0 for asin/acos: Ensure the input is within [-1, 1]. Adjust your upstream normalization.
- Unexpected magnitude for tan near 90° (or π/2): This is expected due to tangent's asymptotes. Consider clamping the input away from asymptotes or using validation.
- Angles seem off by a factor of π/180: Verify the use_degrees setting matches your input and desired output units.
- Output units for inverse operations are not what you expect: Check use_degrees; set it to True to receive inverse results in degrees, or False for radians.
- Input rejected or clipped: The node enforces a numeric range of [-360, 360]. Scale your input accordingly.
