# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs logarithmic and exponential operations on a single floating-point input. It supports custom-base logarithms, natural logarithms, base-10 and base-2 logs, as well as exponential functions with bases e and 2. Invalid inputs such as non-positive values for logarithms or invalid bases are handled safely by returning a fallback result instead of raising errors.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltlogarithmicoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to transform numeric values using logarithmic scaling or exponential growth/decay inside a Salt workflow. Typical scenarios include compressing wide dynamic ranges (such as intensities or magnitudes) into a log scale, computing decibel-like measures with log10, analyzing binary scaling using log2, or shaping control curves with exp or exp2. Place it downstream of FLOAT-producing nodes like "Float Math Operation" or "Comparison Operation" and upstream of nodes that consume numeric parameters (for example, "Rounding Operation" or "Advanced Math Operation"). Choose the operation based on your goal: use "log" for a custom-base logarithm, "ln" for natural log, "log10" for base-10, and "log2" for base-2. Use "exp" to compute e^value and "exp2" to compute 2^value. Ensure that `value` is strictly positive for all log operations, and that `base` is strictly positive and not equal to 1 for the custom-base "log". If needed, preprocess values using clamping or absolute value with other math nodes so they fall within valid ranges and avoid fallback outputs.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input to transform. For logarithmic operations (log, ln, log10, log2) this must be strictly positive; for exponential operations (exp, exp2) it may be any real number. The UI range is 0.001 to 1e10 with a step of 0.001.</td><td style="word-wrap: break-word;">1000.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Base used only when operation is "log" (custom-base logarithm). It must be strictly positive and not equal to 1; otherwise the node treats the input as invalid and returns 0.0. The UI range is 0.001 to 1e10 with a step of 0.001. This input is ignored for ln, log10, log2, exp, and exp2.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["log", "ln", "log10", "log2", "exp", "exp2"]</td><td style="word-wrap: break-word;">Selects which function to apply. "log": custom-base logarithm log_base(value); "ln": natural logarithm; "log10": base-10 log; "log2": base-2 log; "exp": e raised to value; "exp2": 2 raised to value. For all log variants, value must be > 0, and for "log" the base must be > 0 and != 1. When these constraints are violated, the node returns 0.0 instead of throwing an error.</td><td style="word-wrap: break-word;">"log10"</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected logarithmic or exponential operation, as a single FLOAT. This value can be fed into downstream nodes as a control parameter, threshold, scale factor, or other numeric input. When inputs are invalid for the chosen operation, the node returns 0.0 as a safe fallback.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Uses standard floating-point math and executes in constant time, adding negligible overhead even in large graphs.
- **Limitations**: All logarithmic operations (log, ln, log10, log2) require a strictly positive value. If value <= 0, the node returns 0.0 and logs a warning internally rather than raising an exception.
- **Limitations**: For the custom-base "log" operation, base must be > 0 and != 1. An invalid base causes the node to output 0.0, which can flatten downstream behavior if not anticipated.
- **Behavior**: For "exp" and "exp2", large positive input values can generate extremely large outputs that may exceed the practical numeric range expected by other nodes. Clamp or scale inputs before this node where necessary.
- **Behavior**: The base input has no effect for ln, log10, log2, exp, or exp2; it is only used when operation is set to "log".

## Troubleshooting
- **Common Error 1**: Result is always 0.0 when using logarithmic operations. Cause: value is non-positive, or for "log", base is <= 0 or == 1. Fix: ensure value > 0 and choose base > 0 and != 1, potentially by clamping or sanitizing inputs upstream.
- **Common Error 2**: Output becomes extremely large for "exp" or "exp2". Cause: input value is too large in the positive direction, causing exponential blow-up. Fix: restrict value to a smaller range (for example, [-10, 10]) via earlier math or comparison nodes.
- **Common Error 3**: Adjusting the base slider has no visible effect. Cause: operation is set to ln, log10, log2, exp, or exp2, where base is ignored. Fix: set operation to "log" if you need a custom base; otherwise, the base setting can be ignored.
- **Common Error 4**: Downstream nodes misbehave because the result from this node can be negative when using logs with values less than the base (for example, log10(0.1) = -1). Fix: clamp or offset the result using comparison or math nodes, or configure downstream nodes to handle negative values appropriately.
