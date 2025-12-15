# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies standard and hyperbolic trigonometric functions to a numeric input. Supports sine, cosine, tangent, their inverse counterparts, and hyperbolic variants. You can choose whether the input (and inverse outputs) are handled in degrees or radians.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need trigonometric calculations within a workflow, such as converting angles to sine/cosine values, computing tangents, or retrieving angles from ratios via inverse functions. Set use_degrees to match your angle convention. For inverse functions, the result will be returned in degrees if use_degrees is enabled.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input for the selected operation. Interpreted as degrees for sin/cos/tan when use_degrees is true; otherwise radians. For asin/acos, the value must be within [-1, 1].</td><td style="word-wrap: break-word;">30.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The trigonometric function to apply. Options: sin, cos, tan, asin, acos, atan, sinh, cosh, tanh.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, sin/cos/tan interpret input in degrees and inverse functions (asin/acos/atan) return results in degrees. If false, values are treated as radians and inverse results are in radians.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected trigonometric operation. For inverse functions, units depend on use_degrees.</td><td style="word-wrap: break-word;">0.5</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Angle units**: When use_degrees is true, input to sin/cos/tan is treated as degrees and inverse outputs are returned in degrees; otherwise all are in radians.
- **Domain checks**: asin and acos require the input to be within [-1, 1]. Out-of-range inputs return 0.0.
- **Tangent behavior**: tan near odd multiples of 90° (or π/2 radians) can produce very large magnitudes due to asymptotes.
- **Hyperbolic functions**: sinh, cosh, tanh do not use angle unit conversions.
- **Input range**: The input UI range is limited to [-360, 360] with a step of 0.001.

## Troubleshooting
- **Result is 0.0 unexpectedly**: For asin/acos, ensure the input is within [-1, 1].
- **Unexpected magnitude for tan**: Check that the angle is not near 90° + k*180° (or π/2 + k*π). Slight adjustments may stabilize the result.
- **Wrong units in results**: Verify the use_degrees setting matches your intended input and output units.
- **Inverse returns degrees when radians expected**: Set use_degrees to false to obtain inverse results in radians.
