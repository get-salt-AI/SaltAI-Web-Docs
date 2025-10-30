# Lazy Index Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and forwards exactly one of up to 10 inputs by index, without evaluating the others until needed. The node is lazy: only the input matching the chosen index is computed and passed through, which helps avoid unnecessary processing on non-selected branches.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Condition/SaltLazyIndexSwitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node as a multiplexer to route one of several candidate values (of any type) based on an index. It’s ideal for choosing between multiple images, prompts, models, or other resources at runtime while preventing computation of unselected branches.

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
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based selector for which input value to forward (0 selects value0, 1 selects value1, etc.). Valid range is 0–9.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 0. Evaluated lazily only if index is 0.</td><td style="word-wrap: break-word;">An image tensor or any other data type</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 1. Evaluated lazily only if index is 1.</td><td style="word-wrap: break-word;">A text prompt or any other data type</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 2. Evaluated lazily only if index is 2.</td><td style="word-wrap: break-word;">A model reference or any other data type</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 3. Evaluated lazily only if index is 3.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 4. Evaluated lazily only if index is 4.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 5. Evaluated lazily only if index is 5.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 6. Evaluated lazily only if index is 6.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 7. Evaluated lazily only if index is 7.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 8. Evaluated lazily only if index is 8.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 9. Evaluated lazily only if index is 9.</td><td style="word-wrap: break-word;">Any supported data type</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected input corresponding to the provided index. The output type matches whatever type the chosen input provides.</td><td style="word-wrap: break-word;">If index=2 and value2 is an image, the output is that image</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Lazy evaluation**: Only the branch matching the current index is computed; all others are not evaluated.
- **Index range**: Valid indices are 0–9. Ensure the chosen index corresponds to a connected input.
- **Type consistency downstream**: Because the output type depends on the selected input, ensure downstream nodes can handle the possible types you may route.
- **Missing input**: If the selected valueN is not connected or unavailable, the output will be empty/None.

## Troubleshooting
- **No output when selecting an index**: Verify the corresponding valueN input is connected and produces a value.
- **Downstream type errors**: Ensure all potential routed inputs are of a type acceptable to downstream nodes, or add type-specific branches before merging.
- **Unexpected branch computation**: Check that only the selected index is active; the node is lazy and should not trigger unselected branches.
- **Index selects an unconnected slot**: Connect the matching valueN or change the index to a connected input.
