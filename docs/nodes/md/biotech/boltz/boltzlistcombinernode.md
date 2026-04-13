# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple BOLTZ objects (such as sequences, constraints, templates, and properties) into a single ordered list. Accepts up to 50 inputs and flattens any inputs that are already lists, preserving their original order. If no inputs are provided, the node raises an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building individual BOLTZ components (sequences, constraints, templates, properties) to merge them into one list before generating a final Boltz YAML configuration or submitting a prediction job. Connect outputs from Boltz builder nodes (or other nodes that emit BOLTZ objects or lists of them) into the numbered inputs; the node appends each input in order and flattens list inputs into the final combined_list output.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First BOLTZ object or list of BOLTZ objects to include in the combined output. At least this input must be connected for the node to run.</td><td style="word-wrap: break-word;">A single sequence object or a list like [sequence1, sequence2]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second BOLTZ object or list of BOLTZ objects appended after input_1. Only becomes relevant once input_1 is connected.</td><td style="word-wrap: break-word;">A constraint object or [constraint1, constraint2]</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third BOLTZ object or list of BOLTZ objects appended after input_2. Subsequent numbered inputs behave the same way up to input_50.</td><td style="word-wrap: break-word;">A template object or [templateA, templateB]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single flattened list containing all provided BOLTZ objects in the order they were connected. List-type inputs are expanded so their elements appear individually in this list.</td><td style="word-wrap: break-word;">[sequence1, sequence2, constraint1, templateA, propertyX]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input fan-out**: If an input provides a list, each item is appended individually to the combined_list; single objects are appended as-is.
- **Minimum input requirement**: At least one input (typically input_1) must be connected; otherwise the node raises an error.
- **Progressive input visibility**: Higher-numbered inputs are hidden until the previous input is connected (for example, input_2 appears after input_1 is set).
- **No type validation**: The node does not validate object types; only connect valid BOLTZ objects that downstream nodes expect.
- **Order preservation**: Items are ordered first by input index (input_1, then input_2, and so on), then by their order within any list provided to that input.
- **Input limit**: A maximum of 50 inputs are supported (input_1 through input_50). If you need more, pre-combine items with upstream logic or additional combiner nodes.
- **Shallow flattening**: Only the top level of each input is flattened. Lists-of-lists remain nested within their parent item.

## Troubleshooting
- **Issue 1: Error 'At least one input is required'**: Connect at least one BOLTZ object (or list of objects) to input_1 before executing the node.
- **Issue 2: Inputs after input_1 are not visible**: Connect input_1 first; each subsequent input (input_2, input_3, and so on) becomes available only after the previous is set.
- **Issue 3: Output contains nested lists unexpectedly**: The node only flattens the top-level inputs. Avoid passing lists-of-lists unless you intentionally want nested structures to remain.
- **Issue 4: Unexpected item order in combined_list**: Remember that items appear in the order of inputs (input_1 before input_2, etc.) and in the original order within each list input.
- **Issue 5: Some inputs appear to be ignored**: Verify that you are not exceeding the 50-input limit and that each connected input is actually producing data.
- **Issue 6: Downstream node errors on types**: Since this node does not enforce types, double-check that all items in combined_list are valid BOLTZ objects acceptable to your downstream Boltz nodes.
