# Accumulation Get Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node accesses a specific item from an accumulation structure using a zero-based index. It expects a valid ACCUMULATION (usually produced by other list/accumulation logic nodes) and returns the element at the requested position as a dynamic-typed output. If the index is invalid or the accumulation is malformed, it safely returns a null value instead of breaking the workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationgetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an accumulation of items (texts, images, numbers, or other objects) and need to extract exactly one element at a specific position. It typically appears downstream of nodes that build or transform accumulations, such as "Accumulate", "Accumulation Head", "Accumulation Tail", or "List to Accumulation". A common pattern is: data generation or collection → "Accumulate" (possibly over multiple iterations) → optional "Accumulation Get Length" to compute bounds → "Accumulation Get Item" with a chosen index → processing nodes that work on a single item. This is useful for selecting a particular variant in a batch, picking a specific step result, or routing a single element for further processing. For robust workflows, pair this node with "Accumulation Get Length" to avoid out-of-range indices, and make sure downstream nodes can handle the dynamic type of the selected item.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation structure from which the item will be retrieved. Internally this is expected to be an object that contains a list of items under the key "accum". It should be produced by nodes that output ACCUMULATION, such as "Accumulate", "Accumulation to List", or "List to Accumulation". If the structure does not contain a valid list under "accum", item retrieval may fail and return null.</td><td style="word-wrap: break-word;">{"accum": ["draft_prompt_v1", "draft_prompt_v2", "final_prompt"]}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the item to retrieve from the accumulation's internal list. 0 refers to the first element, 1 to the second, and so on. Indices that are negative or greater than or equal to the length of the accumulation will cause an internal error and result in a null item output.</td><td style="word-wrap: break-word;">1</td></tr>
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
<tr><td style="word-wrap: break-word;">item</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The element stored at the specified index in the accumulation. The type is dynamic (WILDCARD) and matches whatever was stored: text, image, number, or other supported object type. If the index is out of range or the accumulation is malformed, this output will be null, and downstream nodes must be prepared to handle that.</td><td style="word-wrap: break-word;">"draft_prompt_v2"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Retrieving a single item by index is constant-time and very lightweight; performance concerns usually come from upstream accumulation building or downstream processing, not this node.
- **Limitations**: The node assumes the accumulation contains a list-like value under the key "accum"; if this structure is missing or corrupted, the node cannot repair it and will return a null item.
- **Behavior**: When an index is invalid (out of range) or the accumulation does not contain the requested element, the node logs an internal error and returns a null output instead of interrupting the workflow.
- **Behavior**: Because the output type is WILDCARD, downstream nodes must be compatible with the actual stored type; there is no automatic type conversion or validation.

## Troubleshooting
- **Item output is null when I expect a value**: Verify that the index is within the valid range. Insert an "Accumulation Get Length" node before this node to check the length and ensure 0 ≤ index < length. Also confirm that the accumulation actually contains items.
- **Downstream node fails due to unexpected None or type mismatch**: The selected index might be invalid, or the stored item type may not match what the downstream node expects. Inspect the accumulation contents upstream, and ensure the index points to an item of the correct type.
- **Always getting the same item regardless of loop or iteration**: Confirm how you set or compute the index. If the index is a constant value or not updated in your loop logic, the node will always return the same position from the accumulation.
- **Accumulation appears empty or not recognized**: Make sure you are using nodes that output ACCUMULATION (like "Accumulate" or "List to Accumulation") rather than raw Python-like lists or other structures. If you start from a list, convert it first with "List to Accumulation" before using this node.
