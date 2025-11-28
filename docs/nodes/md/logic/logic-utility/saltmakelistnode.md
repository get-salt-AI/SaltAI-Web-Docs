# Make List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a single list from multiple inputs. Only the inputs that are actually connected are included, allowing you to mix different data types. Useful for aggregating values into one collection for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltmakelistnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to bundle several values into a single list, for example to feed a list processor, iterator, or batch operation. Connect any combination of the available value inputs; unconnected inputs are ignored.

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
<tr><td style="word-wrap: break-word;">value1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First value to include in the list. Accepts any supported type (numbers, text, images, models, etc.).</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 2 to include in the list.</td><td style="word-wrap: break-word;">"hello"</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 3 to include in the list.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 4 to include in the list.</td><td style="word-wrap: break-word;"><image></td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 5 to include in the list.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 6 to include in the list.</td><td style="word-wrap: break-word;">{"key": "value"}</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 7 to include in the list.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 8 to include in the list.</td><td style="word-wrap: break-word;"><model></td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value 9 to include in the list.</td><td style="word-wrap: break-word;">null</td></tr>
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
<tr><td style="word-wrap: break-word;">list</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">A list containing all connected input values, in order by socket index.</td><td style="word-wrap: break-word;">[42, "hello", 3.14]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Only connected inputs are included**: Unconnected value inputs are skipped and not added to the output list.
- **Order is preserved**: Items appear in the list in ascending order of their input sockets (value1, value2, ..., value9).
- **Mixed types are allowed**: You can combine any supported types in one list.
- **List-of-lists is not flattened**: If you pass a list as a value, it will be included as a single element.
- **Error handling**: On internal errors, the node returns an empty list and logs the error.
- **Number of inputs**: Up to 9 inputs are available (value1 through value9).

## Troubleshooting
- **Missing items in output**: Ensure the corresponding input socket is connected; unconnected sockets are ignored.
- **Downstream type errors**: Some nodes expect uniform element types. If a downstream node fails, verify the list contains the expected types.
- **Unexpected order**: Items are ordered by socket index, not connection time. Reconnect to the intended sockets to correct ordering.
- **Empty list output**: If you receive an empty list unexpectedly, verify at least one input is connected and check logs for errors.
