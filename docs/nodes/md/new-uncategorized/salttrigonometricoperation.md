# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies a selected trigonometric or inverse trigonometric function to a numeric input. Supports sine, cosine, tangent, their inverses (asin, acos, atan), and hyperbolic variants (sinh, cosh, tanh). Can interpret the input as degrees for forward trig functions and optionally return inverse trig results in degrees.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need trigonometric calculations in a workflow, such as converting angles to ratios, computing angles from ratios, or working with hyperbolic functions. Set whether the input angle is given in degrees via use_degrees for sin/cos/tan, and choose the appropriate operation from the list. For inverse functions, ensure the input value is within the valid domain.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input. For sin/cos/tan, this is the angle; its unit depends on use_degrees. For inverse functions, this is a unitless ratio in the domain [-1, 1]. For hyperbolic functions, this is a real number argument.</td><td style="word-wrap: break-word;">45.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The trigonometric operation to perform. One of: sin, cos, tan, asin, acos, atan, sinh, cosh, tanh.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, the input for sin/cos/tan is interpreted as degrees. For inverse functions, when true, the output angle is returned in degrees; when false, the output is in radians.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric result of the chosen trigonometric operation. For inverse functions, the angle is in degrees if use_degrees is true, otherwise radians.</td><td style="word-wrap: break-word;">0.70710678</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Angle units (sin/cos/tan)**: If use_degrees is true, the node interprets the input angle in degrees; otherwise it treats it as radians.
- **Angle units (asin/acos/atan)**: The input must be a unitless ratio in [-1, 1]. The output angle is returned in degrees when use_degrees is true, or radians when false.
- **Domain checks**: asin and acos validate that the input is within [-1, 1]. Out-of-range inputs return 0.0.
- **Edge behavior for tan**: Near odd multiples of 90 degrees (or π/2 radians), tan grows very large in magnitude and can produce extreme values.
- **Hyperbolic functions**: sinh, cosh, tanh accept any real input and return real outputs.
- **Error handling**: On invalid inputs or unknown operations, the node returns 0.0.

## Troubleshooting
- **Result is 0.0 for inverse functions**: Ensure the input value is within [-1, 1].
- **Unexpected magnitude for tan**: Avoid angles near 90°, 270° (or π/2, 3π/2 radians) where tan is undefined and numerically unstable.
- **Units confusion**: For sin/cos/tan, confirm whether your angle is degrees or radians and set use_degrees accordingly. For asin/acos/atan, remember the input is a ratio, not an angle.
- **Clamped UI range**: The input field UI ranges from -360 to 360, which is suitable for many angle uses but does not limit function domains (e.g., ratios for inverse functions); ensure your value is meaningful for the selected operation.
