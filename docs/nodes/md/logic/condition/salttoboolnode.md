# To Bool

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltToBoolNode converts arbitrary inputs into a boolean value for driving logic and branching. Tensors are treated as False only when all elements are exactly zero and True otherwise, while non-tensor values use standard truthiness conversion. If conversion fails, it defaults to True, and an optional invert flag lets you flip the final result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/salttoboolnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to turn a value from elsewhere in your Salt workflow into a clean True/False signal. Common scenarios include: checking if a model output tensor (such as an image or feature map) is effectively empty or non-empty, turning numeric scores or counters into a boolean gate, or interpreting configuration or user inputs (strings, numbers, lists) as a condition. It typically appears just before branching or logic nodes that expect a BOOLEAN input, such as "IfConditionSelector" or "SaltBoolOperationNode". Upstream, it can consume outputs from model inference nodes, numerical processing nodes, or condition nodes like "SaltIntConditions", "SaltFloatConditions", and "SaltStringConditions". A typical pattern is: data-producing node → SaltToBoolNode (with optional invert=True) → IfConditionSelector to route between success and fallback branches. Prefer using the "invert" input when you want the opposite of the computed condition instead of adding an extra NOT step elsewhere in the graph.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Arbitrary value to interpret as a boolean. If it is a tensor, the node checks its minimum and maximum elements: it returns False only if both are exactly zero, meaning every element is zero; any non-zero element yields True. For non-tensor types, it uses standard truthiness semantics (similar to Python's bool()): zero, empty strings, and empty collections are treated as False, while non-zero numbers, non-empty strings, and non-empty collections are treated as True. If conversion raises a type- or value-related error, the node falls back to treating the value as True.</td><td style="word-wrap: break-word;">A tensor representing an image from a generation node (True if any pixel is non-zero), a numeric score 0 from a metric node (False because it is zero), or an empty string "" from a prior text step (False because it is empty).</td></tr>
<tr><td style="word-wrap: break-word;">invert</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Optional flag that inverts the computed boolean result. When False (default), the node outputs the direct boolean interpretation of "value". When True, it negates the result (True becomes False, False becomes True). This is useful for conditions such as "no data", "not successful", or when you conceptually want the opposite of the default interpretation without inserting a separate negation node.</td><td style="word-wrap: break-word;">True, when you want an empty string (which evaluates to False) to cause the node to output True so a fallback branch is triggered in a following IfConditionSelector.</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The final boolean computed from the input value after optional inversion. This is a simple scalar True/False output, suitable for feeding into downstream logic nodes, selectors, or boolean combiners such as SaltBoolOperationNode. It does not preserve any structure from the original value.</td><td style="word-wrap: break-word;">True when given a tensor with at least one non-zero element or a non-empty text like "enabled"; False when given an all-zero tensor, the integer 0, an empty list [], or when a True value is inverted via invert=True.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: For tensor inputs, the node computes min and max values to check if all elements are zero; on very large tensors this adds a small but non-negligible cost, so avoid piping oversized intermediate tensors through it when not needed.
- **Limitations**: Non-tensor values that do not convert cleanly (for example, some complex or custom objects) are treated as True by the fallback path, which may not match strict validation requirements; normalize or validate such inputs upstream if you need more precise control.
- **Behavior**: Tensor evaluation is strictly binary: any non-zero element, even very small numerical noise, causes the result to be True. If you need threshold-based semantics (e.g., ignore tiny noise), apply thresholding or rounding to the tensor before this node.
- **Behavior**: On unexpected internal errors, the node logs the issue and returns False, so error states can manifest as a False condition; design downstream branches with this conservative failure mode in mind.

## Troubleshooting
- **Result is True for complex or custom objects**: If you pass a non-empty custom object and see True when you expected False, remember that many objects are truthy and failed conversions fall back to True. Convert such values to simple types (numbers, strings, or explicit booleans) upstream before using this node.
- **Tensor-based result is always True due to small noise**: When a nearly-zero tensor still yields True, it is because any non-zero entry counts as True. Insert an upstream step that clamps or thresholds values below a chosen magnitude to zero before feeding the tensor into SaltToBoolNode.
- **Branches seem reversed in downstream logic**: If an IfConditionSelector or similar node appears to choose the opposite branch from what you intended, check the "invert" input on SaltToBoolNode. Setting invert=True flips the meaning of the condition and can accidentally reverse control flow.
- **Unexpected False outputs where True is expected**: Because internal errors cause this node to return False, a persistent False result may indicate an exception during conversion. Test with simpler inputs or log intermediate values to identify problematic types or values being passed into the node.
