# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies standard and hyperbolic trigonometric functions to a single numeric input. Supports sine, cosine, tangent, their inverse functions, and hyperbolic variants. Can interpret inputs as degrees or radians and returns a single float result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compute trigonometric values in math-heavy flows, geometry calculations, signal processing, or angle conversions. Set use_degrees to control whether the input value is interpreted as degrees (True) or radians (False). For inverse functions, if use_degrees is True, the result is returned in degrees; otherwise, the result is in radians.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input for the selected trigonometric operation. Interpreted as degrees if use_degrees is True, otherwise as radians. For asin/acos, the effective input must be within [-1, 1].</td><td style="word-wrap: break-word;">30.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The trigonometric function to apply. Options: sin, cos, tan, asin, acos, atan, sinh, cosh, tanh.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, value is treated as degrees for sin/cos/tan and inverse results (asin/acos/atan) are returned in degrees. If False, value is treated as radians for sin/cos/tan and inverse results are returned in radians.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed trigonometric result as a floating-point number.</td><td style="word-wrap: break-word;">0.5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input domain for asin/acos**: The effective input to asin/acos must be within [-1, 1]. If out of range, the node returns 0.0.
- **Units handling**: sin/cos/tan use the input units based on use_degrees. For inverse functions (asin/acos/atan), the output unit follows use_degrees (degrees if True, radians if False).
- **Hyperbolic functions**: sinh/cosh/tanh ignore degree settings; they always treat value as a unitless real number.
- **Numerical extremes**: tan near odd multiples of 90° (or π/2 radians) can produce very large magnitudes due to asymptotes.
- **Precision**: Floating-point rounding can cause slight deviations from expected exact values (e.g., cos(90°) may yield a very small non-zero number).

## Troubleshooting
- **Result is 0.0 for asin/acos**: Ensure the effective input is within [-1, 1]. Adjust value or check use_degrees so the domain is valid.
- **Unexpected magnitude from tan**: Inputs near 90° + k·180° (or π/2 + k·π radians) cause tan to approach infinity. Slightly adjust the angle away from the asymptote.
- **Output unit confusion**: If inverse results appear unexpected, verify use_degrees. True returns inverse results in degrees; False returns them in radians.
- **Very small residuals instead of exact zeros**: This is normal floating-point behavior. Consider rounding the result if exact zeros are required.
