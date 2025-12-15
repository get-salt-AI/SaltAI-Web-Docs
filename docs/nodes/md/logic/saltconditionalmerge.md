# Merge If Branches

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges results from multiple conditional branches into a single output. It selects the first input that is present (non-null), prioritizing lower-numbered inputs when more than one branch produced a value. Supports any data type passed from upstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltconditionalmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to rejoin workflow paths after an If (or other conditional) split. Connect the final output of each branch (e.g., true/false) to the corresponding inputs on this node. Downstream nodes can then consume a single merged result regardless of which branch executed.

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
<tr><td style="word-wrap: break-word;">branch_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Result from the first conditional branch (e.g., the 'true' path). If provided and non-null, it has priority over branch_2.</td><td style="word-wrap: break-word;">Output from the True branch of an If node (e.g., 42)</td></tr>
<tr><td style="word-wrap: break-word;">branch_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Result from the second conditional branch (e.g., the 'false' path). Used if branch_1 is not provided or is null.</td><td style="word-wrap: break-word;">Output from the False branch of an If node (e.g., "fallback result")</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The selected result from the connected branches. Returns the first non-null value among the inputs, or null if neither branch provided a value.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Selection priority**: If multiple branch inputs are present, the node returns the value from branch_1 before branch_2.
- **Optional inputs**: Both inputs are optional—only the branch(es) that executed need to be connected.
- **Type flexibility**: Accepts and outputs any data type; ensure downstream nodes can handle the merged type.
- **Null behavior**: If no branch produces a value, the node outputs null.
- **Typical wiring**: Connect outputs from an If node (e.g., index 0 to branch_1, index 1 to branch_2) to this node.

## Troubleshooting
- **Output is null**: Ensure at least one branch is connected and produces a non-null value. Verify the condition routing upstream.
- **Unexpected branch chosen**: Remember branch_1 has priority. Swap connections if you need branch_2 to take precedence when both are present.
- **Downstream type errors**: Confirm the downstream node supports the type produced by the executing branch. Consider normalizing types across branches.
- **Unconnected inputs warnings**: It's safe to leave an input unconnected if that branch may not execute, but ensure at least one branch will provide a value at runtime.
