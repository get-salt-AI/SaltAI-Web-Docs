# Make List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Aggregates multiple inputs into a single list output. Only connected inputs are included, preserving their order. Supports mixed data types, enabling you to bundle heterogeneous values together.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltmakelistnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine several individual values into one list for downstream processing (for example, batching prompts, grouping images, or collecting parameters). Connect any number of inputs and pass the resulting list to nodes that iterate over or consume list data.

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
<tr><td style="word-wrap: break-word;">value1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First value to include in the list. Accepts any type (text, number, image, model reference, etc.).</td><td style="word-wrap: break-word;">hello world</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second value to include in the list, if connected.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third value to include in the list, if connected.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth value to include in the list, if connected.</td><td style="word-wrap: break-word;">image_reference</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth value to include in the list, if connected.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Sixth value to include in the list, if connected.</td><td style="word-wrap: break-word;">another string</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Seventh value to include in the list, if connected.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Eighth value to include in the list, if connected.</td><td style="word-wrap: break-word;">{'param': 'value'}</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Ninth value to include in the list, if connected.</td><td style="word-wrap: break-word;">['a', 'b']</td></tr>
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
<tr><td style="word-wrap: break-word;">list</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">A list containing all connected input values in order. The output is a list-type value suitable for list-processing nodes.</td><td style="word-wrap: break-word;">['hello world', 42, True]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Connected-only aggregation**: Only inputs that are actually connected are included; unconnected sockets are skipped.
- **Order preserved**: The output list preserves the order of the input sockets (value1, value2, ...).
- **Mixed types supported**: You can combine different data types in the same list.
- **Socket count**: This node provides sockets up to value9. Although the description references 'up to 10', the available inputs are value1 through value9.
- **Empty result on error**: If an internal error occurs during list creation, the node returns an empty list.

## Troubleshooting
- **Output is empty**: Ensure at least value1 is connected. Only connected inputs are included; unconnected sockets are ignored.
- **Unexpected order**: Verify which inputs are connected and their socket positions; the list reflects the socket order.
- **Downstream type errors**: If a following node expects a uniform type, avoid mixing types in this list or validate compatibility.
- **Too many items**: If you need more than the available sockets, chain multiple Make List nodes (e.g., make sublists, then concatenate using list operations).
- **Null or missing items**: If an item appears missing, check for unconnected sockets; if null appears, verify the upstream node actually outputs a value.
