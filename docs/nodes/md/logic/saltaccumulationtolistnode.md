# Accumulation to List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes an ACCUMULATION object (the structure produced by other accumulation-focused nodes) and returns the underlying list of collected items. It unwraps the internal "accum" field when present and otherwise yields an empty list, providing a safe way to transition from the accumulation format back to a standard list. This is useful when you need to feed accumulated values into downstream logic that expects a normal list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationtolistnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have built up an ACCUMULATION over time (for example using Accumulate, List to Accumulation, or Accumulation Set Item) and need a regular list for downstream processing. Typical workflows: (1) Collect prompts, IDs, or configuration objects across multiple branches with Accumulate, then convert to a list here to loop or select items with list-aware nodes; (2) Convert an ACCUMULATION edited via Accumulation Get Item/Set Item back into a list for batch processing or conditional routing; (3) Bridge between nodes that operate in the ACCUMULATION domain and nodes that only understand raw lists. Place it near the point in your pipeline where you are done appending or mutating the accumulation and ready to treat the data as a finalized list.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object to unwrap. It should be an ACCUMULATION structure, typically a dictionary with an "accum" key containing a list of items, as produced by nodes like Accumulate or List to Accumulation. If the input is not a dict with an "accum" key, the node will not raise an error but will return an empty list instead.</td><td style="word-wrap: break-word;">{"accum": ["prompt: cinematic portrait", "prompt: wide landscape", "prompt: macro detail"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulated_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single output value that is itself a list containing all items stored in the ACCUMULATION, in insertion order. If the input is not a valid ACCUMULATION or is missing the "accum" key, this will be an empty list. Downstream nodes can treat this as a standard list of values, such as text prompts, numeric parameters, or object references.</td><td style="word-wrap: break-word;">["prompt: cinematic portrait", "prompt: wide landscape", "prompt: macro detail"]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node returns the list taken directly from the ACCUMULATION structure; for large collections this avoids copying but means that mutating the returned list in-place can affect other parts of the workflow that share the same ACCUMULATION.
- **Limitations**: Only properly structured ACCUMULATION objects (dicts with an "accum" list) are unwrapped; passing a plain list or other types will not be auto-detected and will result in an empty list output.
- **Behavior**: Although the return type is "*", the actual runtime value is always a list; OUTPUT_IS_LIST is false, meaning you get one output value that is a list, not multiple outputs.
- **Behavior**: On any unexpected error while reading the accumulation, the node logs the issue internally and returns an empty list instead of interrupting execution.

## Troubleshooting
- **Symptom: Output is always an empty list**: Ensure the upstream node produces a proper ACCUMULATION (e.g., from Accumulate or List to Accumulation) and that the data structure contains an "accum" key with a list. If you are passing a plain list directly, convert it first with List to Accumulation.
- **Symptom: Downstream nodes complain about type mismatch**: Some consumers may expect a single item, not a list. Insert selection logic after this node (such as converting back to ACCUMULATION and using Accumulation Get Item, or a list-indexing/select node) to pick the specific element you need.
- **Symptom: Changes to the resulting list affect other branches**: If you modify accumulated_list inside a custom node or script and see side effects elsewhere, avoid modifying it in-place. Instead, create a new list (for example by copying it) and work on that copy to prevent unintended shared mutations.
