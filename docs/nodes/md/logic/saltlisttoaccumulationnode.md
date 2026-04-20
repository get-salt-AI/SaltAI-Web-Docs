# List to Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts a standard value or list into Salt's ACCUMULATION structure, which is a dictionary containing an "accum" list. If the input is already a list, it is used directly; if the input is a single value, it is wrapped into a one-element list. It is the main entry point for turning arbitrary list-like data into a form that other accumulation nodes can consume.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltlisttoaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a list (or a single value) and need to feed it into the accumulation-based list processing utilities. Upstream, it typically receives either a raw list from a data fetch, JSON parse, or batch generation node, or a single item that you want to treat as a one-element sequence. Downstream, the ACCUMULATION output is designed to connect to nodes such as "Accumulate", "Accumulation Head", "Accumulation Tail", "Accumulation to List", "Accumulation Get Length", "Accumulation Get Item", and "Accumulation Set Item". A common pattern is: obtain a list from some data source → List to Accumulation → operate on the ACCUMULATION (filtering, indexing, modifying) → Accumulation to List to convert it back to a standard list. Prefer this node over manually constructing {"accum": [...]} to ensure consistent structure and compatibility with all accumulation utilities.

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
<tr><td style="word-wrap: break-word;">list</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The input to convert into an ACCUMULATION. If this value is a list, its elements become the internal accumulation list. If it is any other value (string, number, dict, image reference, etc.), it is wrapped into a one-element list. The wildcard type allows lists of any supported Salt data type, but mixing incompatible types within the same list may restrict which downstream nodes can safely consume it.</td><td style="word-wrap: break-word;">["user_123_events.json", "user_456_events.json", "user_789_events.json"]</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An ACCUMULATION object that encapsulates the input under the "accum" key. If the input was a list, the result is {"accum": input_list}. If the input was a single value, the result is {"accum": [input_value]}. This standardized structure is required by the accumulation utility nodes that operate on ordered sequences.</td><td style="word-wrap: break-word;">{"accum": ["user_123_events.json", "user_456_events.json", "user_789_events.json"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only a shallow wrapping of the input and does not clone or transform individual elements, so it is efficient even for large lists.
- **Limitations**: Non-list inputs are always treated as a single element and wrapped into a one-element list; the node does not attempt to parse strings or other scalars into multiple items.
- **Behavior**: The node does not validate element types when the input is an existing list; downstream accumulation nodes may fail or behave unexpectedly if they expect a specific element type.
- **Behavior**: On internal errors, the node falls back to returning an empty accumulation ({"accum": []}), which means downstream nodes will see an empty sequence instead of raising an exception at this step.

## Troubleshooting
- **Common Error 1**: "Downstream accumulation node sees zero items" – This usually means the input to List to Accumulation was invalid or the node encountered an internal error and returned {"accum": []}. Verify that the upstream node outputs the data you expect and that it is not null or malformed.
- **Common Error 2**: "IndexError or None returned from Accumulation Get Item" – This often occurs when you assume more elements than actually exist. After List to Accumulation, use "Accumulation Get Length" to confirm the list size before accessing a specific index.
- **Common Error 3**: "Unexpected single-element list" – If you pass a scalar value (for example, "session_001.log") instead of a list (["session_001.log", "session_002.log"]) into this node, it will wrap it as a one-element accumulation. Ensure the upstream node outputs a list when you intend to process multiple items.
