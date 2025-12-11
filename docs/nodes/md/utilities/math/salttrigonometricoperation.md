# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies a selected trigonometric or hyperbolic function to a numeric input. Supports standard trig (sin, cos, tan), inverse trig (asin, acos, atan), and hyperbolic functions (sinh, cosh, tanh). Can interpret inputs and return inverse-trig outputs in degrees or radians based on a toggle.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compute trigonometric values within math-heavy workflows, control systems, geometry calculations, or signal processing. Select the operation, provide the input value, and choose whether to treat angles as degrees. For inverse functions, when degrees are enabled, outputs are converted to degrees.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input to the selected trigonometric function. If use_degrees is true, this is treated as degrees for sin/cos/tan. For inverse trig, the input should be within the valid domain (e.g., [-1, 1] for asin/acos). Hyperbolic functions treat this as a unitless value.</td><td style="word-wrap: break-word;">45.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">The trigonometric or hyperbolic operation to compute.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, sin/cos/tan interpret input as degrees, and inverse trig outputs (asin/acos/atan) are returned in degrees. If false, circular trig uses radians and inverse trig outputs are in radians.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric result of the selected trigonometric operation after any degree/radian handling.</td><td style="word-wrap: break-word;">0.70710678</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Angle units**: The use_degrees toggle affects circular trig inputs (sin, cos, tan) and inverse trig outputs (asin, acos, atan). Hyperbolic functions (sinh, cosh, tanh) do not use angular units.
- **Domains**: asin and acos require inputs in the range [-1, 1]. Out-of-range inputs will yield a 0.0 result.
- **Tangent behavior**: tan can grow very large near its singularities (e.g., 90° + k*180° in degrees, or (π/2) + k*π in radians).
- **Input range**: The configured input control typically allows values between -360 and 360 for convenience; actual valid ranges depend on the chosen operation.
- **Numeric stability**: Extreme inputs to hyperbolic functions can overflow to very large magnitudes.

## Troubleshooting
- **Result is 0.0 for asin/acos**: Ensure the input value is within [-1, 1]. If using degrees toggle, verify you are providing the correct unit/magnitude.
- **Unexpected magnitude or sign**: Confirm whether use_degrees is correctly set for your input and expectations. Degrees vs. radians mismatches are a common source of errors.
- **Very large or 'infinite' tan results**: Your input is near a tangent singularity. Adjust the angle slightly away from 90° + k*180° (or (π/2)+k*π when in radians).
- **Hyperbolic outputs seem off**: Remember hyperbolic functions do not interpret inputs as angles; ensure your value is appropriate for sinh/cosh/tanh.
- **Clipped or limited input values**: If you cannot enter a needed value, adjust the upstream value generation or modify the node configuration to accommodate your range.
