# Accumulation Set Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node modifies an existing accumulation by replacing the value at a specified index. It accepts an ACCUMULATION structure, an integer index, and a new value, then updates the underlying list-like storage. If the index is invalid or the structure is not as expected, the node logs an error internally and returns the original accumulation unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationsetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to overwrite or correct a particular element inside an existing accumulation during a logic or list-processing workflow. It is typically used after nodes that produce or transform accumulations such as "SaltAccumulateNode", "SaltAccumulationHeadNode", "SaltAccumulationTailNode", or "SaltListToAccumulationNode". Downstream, the updated accumulation is often consumed by nodes like "SaltAccumulationGetItemNode", "SaltAccumulationGetLengthNode", or "SaltAccumulationToListNode" for inspection, iteration, or conversion back to a standard list. A common pattern is: build an accumulation with "SaltAccumulateNode", retrieve and process an item with "SaltAccumulationGetItemNode" and other transformation nodes, then write the processed result back using "SaltAccumulationSetItemNode". Prefer this node when you need random-access updates instead of rebuilding the entire accumulation, and always ensure the index is valid before updating.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation structure to update. This is a specialized container that internally behaves like a list stored under an "accum" key (for example, {"accum": [...]}). It should originate from compatible accumulation nodes so that indexed access is valid and stable.</td><td style="word-wrap: break-word;">{"accum": ["raw introduction paragraph", "draft summary paragraph", "closing paragraph"]}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the element to replace within the accumulation. Valid values range from 0 to length-1 of the underlying accumulation list. Negative indices or indices equal to or greater than the list length will cause an internal error and no modification will be applied.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The new value to store at the given index. This accepts any data type supported by the accumulation (such as text chunks, image references, or intermediate data structures). For reliable downstream processing, keep this type consistent with other elements already in the accumulation.</td><td style="word-wrap: break-word;">"final edited summary paragraph after quality checks"</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation after attempting to update the specified index. If the operation is successful, the element at the given index is replaced with the new value. If an error occurs (such as an invalid index or malformed accumulation), the original accumulation is returned without modification.</td><td style="word-wrap: break-word;">{"accum": ["raw introduction paragraph", "final edited summary paragraph after quality checks", "closing paragraph"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node updates the accumulation in place, which is efficient even for large accumulations. However, any other branch or node that holds a reference to the same accumulation will see the updated value.
- **Limitations**: The node does not create new positions in the accumulation. Providing an index outside the current bounds or a malformed accumulation structure will not extend or repair it; instead, the node logs an error internally and returns the original accumulation.
- **Behavior**: The `value` input is a WILDCARD type, so the node does not enforce uniform types across elements. Inserting values of inconsistent types may cause failures or unexpected behavior in downstream nodes that assume homogeneous item types.
- **Behavior**: Because errors are handled by logging and returning the original accumulation, failures may appear as “no change happened.” It is important to validate indices and, when needed, use "SaltAccumulationGetLengthNode" to compute safe index ranges.

## Troubleshooting
- **Item appears unchanged**: If the element does not seem to update, verify that the `index` is within the valid range by using "SaltAccumulationGetLengthNode" and confirming that 0 ≤ index < length. Also ensure that downstream nodes are connected to the output of this node and not to an earlier version of the accumulation.
- **Downstream node type issues**: If later nodes raise errors or behave unexpectedly, inspect the `value` you set at the index. Ensure it matches the expected type (for example, all elements being text segments if a later node concatenates them). Mixing incompatible types can break downstream logic.
- **Silent failures with complex accumulations**: When working with accumulations produced from multiple branches, confirm that your `accumulation` input actually has the correct structure (for instance, something like {"accum": [...]}) and originates from nodes such as "SaltAccumulateNode" or "SaltListToAccumulationNode". If the structure is wrong, the node will log an error and return it unchanged.
- **Unexpected updates in other branches**: Because the update is in-place, if the same accumulation is reused across branches, updating it here will affect all branches that share it. If you need isolation, consider structuring the workflow so each branch gets its own accumulation instance before applying this node.
