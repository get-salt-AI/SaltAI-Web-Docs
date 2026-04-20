# Trigonometric Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node computes a single trigonometric or hyperbolic function on an input value and returns a floating-point result. It supports sine, cosine, tangent, their inverse (arc) variants, and hyperbolic functions, with a switch to interpret or return angles in degrees or radians. Basic range checks and safe fallbacks are used for invalid or out-of-domain inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/salttrigonometricoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need angle-based or periodic numeric transformations within a Salt workflow, such as generating sinusoidal control signals, converting between ratios and angles, or shaping numeric curves with hyperbolic functions. It is typically placed in the math or utility section of a pipeline, downstream of FLOAT-producing nodes (like parameter sliders or "SaltFloatMathOperation") and upstream of nodes that consume scalar values (for example, geometry, animation, or logic nodes). Select an operation from "sin", "cos", "tan", "asin", "acos", "atan", "sinh", "cosh", or "tanh". The "use_degrees" flag should match your workflow’s angle convention: when true, sin/cos/tan treat the input as degrees and arc functions return results in degrees; when false, the node uses and returns radians. Combine it with nodes such as "SaltFloatMathOperation" for scaling and offsets, "SaltComparisonOperation" to branch on trigonometric results, and "SaltAdvancedMathOperation" for more complex formulas. Standardize on degrees or radians across your pipeline to avoid confusion and inconsistent behavior.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input to the chosen function. For sin/cos/tan, this represents an angle; for asin/acos/atan, it is a ratio whose angle you want; for sinh/cosh/tanh, it is the hyperbolic argument. When use_degrees is true and operation is sin/cos/tan, the value is interpreted as degrees and converted internally to radians; otherwise it is treated as radians. For asin and acos, the value must be within [-1, 1] or the node returns 0.0. The UI default range is [-360.0, 360.0] with a step of 0.001.</td><td style="word-wrap: break-word;">45.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["sin", "cos", "tan", "asin", "acos", "atan", "sinh", "cosh", "tanh"]</td><td style="word-wrap: break-word;">Specifies which trigonometric or hyperbolic function to apply. "sin", "cos", and "tan" compute standard circular functions; "asin", "acos", and "atan" compute inverse (arc) functions; "sinh", "cosh", and "tanh" compute hyperbolic functions. Any unsupported value results in a warning internally and a 0.0 output.</td><td style="word-wrap: break-word;">sin</td></tr>
<tr><td style="word-wrap: break-word;">use_degrees</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Determines whether angles are interpreted and/or returned in degrees or radians. For sin/cos/tan: if true, the input is assumed to be in degrees and converted to radians; if false, the input is treated as radians. For asin/acos/atan: computation is done in radians, but if this flag is true, the result is converted to degrees before output. Hyperbolic functions are not affected mathematically by this flag.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric result of the selected operation. For sin and cos, results are typically within [-1, 1]; tan can produce very large magnitudes near asymptotes; asin/acos/atan return an angle (in degrees if use_degrees is true, otherwise in radians); sinh/cosh/tanh return hyperbolic values, which may grow large for large inputs. Invalid operations or out-of-domain inputs for asin/acos produce 0.0.</td><td style="word-wrap: break-word;">0.70710678</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a single math function evaluation and is very fast, making it suitable for high-frequency or iterative workflows.
- **Limitations**: asin and acos require inputs in [-1, 1]; values outside this range cause the node to log a warning internally and return 0.0, which can flatten your output unexpectedly.
- **Behavior**: The use_degrees flag affects input units for sin/cos/tan and output units for asin/acos/atan, but hyperbolic functions simply use the numeric input as provided.
- **Behavior**: Unsupported operation strings do not raise an external error; the node logs a warning and returns 0.0, so typos in the operation name can silently degrade results.
- **Limitations**: No clamping is applied to large inputs for tan, sinh, cosh, or tanh; these can yield extremely large outputs, which may destabilize downstream custom logic if not handled.

## Troubleshooting
- **Unexpected 0.0 outputs**: Verify that the operation string is exactly one of the supported options and that asin/acos inputs are within [-1, 1]. Also ensure the upstream node provides a non-zero FLOAT.
- **Incorrect angle scale**: If arc outputs seem off by a factor of about 57.3, the use_degrees flag is likely mismatched. Enable it for degree-based workflows and disable it for radian-based workflows.
- **Abrupt spikes or discontinuities**: Large jumps often come from tan near 90°/270° (degrees) or π/2/3π/2 (radians). Avoid these angles or switch to sin/cos for smooth periodic behavior.
- **Very large hyperbolic outputs**: If sinh or cosh produce extremely large values, scale down your input with "SaltFloatMathOperation" or use tanh, which naturally saturates near -1 and 1 for large |value|.
