# Accumulation Set Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node mutates an existing accumulation by setting the element at a specified index to a new value. It expects an accumulation object (with an internal "accum" list), an integer index, and a value of any type, then returns the updated accumulation. If the index is invalid or the structure is malformed, it logs an error and returns the original accumulation unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationsetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to modify a single item inside an existing accumulation without rebuilding the entire list. Typical scenarios include updating a particular step result in a running collection, patching a single configuration entry, or overwriting an earlier intermediate result after additional processing. It is usually placed after nodes that create or transform accumulations, such as SaltAccumulateNode, SaltAccumulationHeadNode, SaltAccumulationTailNode, or SaltListToAccumulationNode, and before consumers like SaltAccumulationGetItemNode, SaltAccumulationToListNode, or logic/control nodes that branch based on the updated data. A common pattern is: start with an accumulation (for example, via SaltAccumulateNode or SaltListToAccumulationNode), modify one or more elements using SaltAccumulationSetItemNode, then read or convert the final structure using SaltAccumulationGetItemNode, SaltAccumulationGetLengthNode, or SaltAccumulationToListNode. For best results, ensure the index is validated (for example by using SaltAccumulationGetLengthNode to check bounds) and that the type of value matches the kind of items stored in the accumulation to keep your pipeline consistent.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object whose internal list will be updated. It must be a valid accumulation, typically a structure containing an "accum" key pointing to an indexable sequence. If the structure is missing or malformed, the node will leave it unchanged.</td><td style="word-wrap: break-word;">{"accum": ["draft_introduction", "draft_body", "draft_conclusion"]}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index into the accumulation's internal list specifying which element to replace. Must be within the range of existing items (0 to length-1). Out-of-range indexes will trigger an internal error log and the accumulation will be returned unchanged.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The new value to store at the specified index. This can be any supported type (text, image, embedding, structured object, and so on), but should generally match the types already contained in the accumulation to avoid inconsistent downstream behavior.</td><td style="word-wrap: break-word;">"revised_draft_body_with_feedback_applied"</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The updated accumulation after replacing the item at the specified index, or the original accumulation if the update failed due to index or structure issues. Downstream nodes can treat this as a standard accumulation, passing it into nodes like SaltAccumulationGetItemNode, SaltAccumulationGetLengthNode, SaltAccumulationHeadNode, SaltAccumulationTailNode, or SaltAccumulationToListNode.</td><td style="word-wrap: break-word;">{"accum": ["draft_introduction", "revised_draft_body_with_feedback_applied", "draft_conclusion"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The operation is an in-place update on an existing list element and is O(1); it is suitable for frequent updates even in longer accumulations.
- **Limitations**: If the index is out of range or the accumulation does not contain an "accum" list, the node logs an error internally and simply returns the original accumulation without change.
- **Behavior**: The node mutates the incoming accumulation object rather than creating a deep copy, which means other branches that hold a reference to the same accumulation may see the updated value.
- **Behavior**: The value input is type-agnostic (WILDCARD); mixing heterogeneous types in a single accumulation may confuse downstream nodes that expect a consistent type.
- **Best Practice**: Use SaltAccumulationGetLengthNode before setting an item if the index might be dynamic, to ensure it falls within valid bounds.

## Troubleshooting
- **Item not updated**: If the item appears unchanged downstream, verify that the index is within bounds (0 to length-1) and that the accumulation indeed contains an "accum" list; consider inserting a SaltAccumulationGetLengthNode and a debug/logging step to confirm.
- **Unexpected value type downstream**: If later nodes error or behave oddly after an update, check that the value type matches the expected item type for that accumulation; for example, do not insert a text object into an accumulation that downstream nodes expect to be all images.
- **Changes affecting other branches**: If another branch of your workflow unexpectedly sees the modified value, remember that the accumulation is mutated in place; if isolation is required, duplicate or rebuild the accumulation before passing it into SaltAccumulationSetItemNode.
