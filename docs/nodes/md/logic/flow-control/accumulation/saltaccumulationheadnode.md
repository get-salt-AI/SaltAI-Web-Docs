# Accumulation Head

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node extracts the first (head) element from an ACCUMULATION structure produced by SALT list/accumulation nodes. It is defensive: if the input is empty, missing, or malformed, it returns null instead of failing. The node is variant-aware, so the returned head has the same type as the items stored in the accumulation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationheadnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have built an ACCUMULATION (for example with Accumulate or List to Accumulation) and need to read just the first item. Place it downstream of nodes that output an ACCUMULATION, such as Accumulate, Accumulation Set Item, or List to Accumulation, in list-processing or iterative workflows. Connect the ACCUMULATION output of those nodes into this node, then route the head output into nodes that operate on a single item, like text-processing, routing/logic nodes, or model inference nodes. A common pattern is: collect multiple values across steps with Accumulate, feed the result into Accumulation Head to inspect or special-case the first entry, and use Accumulation Tail or Accumulation to List to process the remaining items. Prefer this node over manual indexing because it gracefully handles empty accumulations by returning null, which you can handle with conditional logic or default-value nodes.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An ACCUMULATION object containing a list of items under the "accum" key. This should be the direct output of accumulation utilities such as Accumulate or List to Accumulation. If the object is not a dictionary with an "accum" list, or if that list is empty, the node returns null. The items in the list can be any supported type (for example: text strings, image references, numeric values, or structured records).</td><td style="word-wrap: break-word;">{"accum": ["Parsed user request", "Conversation context summary", "Draft assistant reply"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">head</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The first element from the accumulation’s "accum" list, or null if the list is empty or the ACCUMULATION is invalid. The concrete runtime type matches whatever was accumulated (text, image, number, object, etc.), and this value can be passed directly to downstream nodes that accept that type.</td><td style="word-wrap: break-word;">"Parsed user request"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node reads only the first element and does not copy or transform the full list, so it remains efficient even for large accumulations.
- **Limitations**: It expects the canonical ACCUMULATION structure {"accum": [...]}. If you pass a plain list or a differently shaped object, the node will not coerce it and will return null.
- **Behavior**: When the internal "accum" list is empty, missing, or of the wrong shape, the node returns null instead of raising errors; downstream flows must be prepared to handle a null head.
- **Behavior**: The output is WILDCARD and its actual type depends entirely on the items accumulated; ensure downstream nodes are compatible with the expected item type.
- **Integration**: Designed to work with the SALT/Logic/Lists family, especially Accumulate, Accumulation Tail, Accumulation to List, List to Accumulation, and Accumulation Get Item for advanced list workflows.

## Troubleshooting
- **Head output is null when a value is expected**: Check that the upstream node really produced a non-empty ACCUMULATION. Verify that Accumulate (or similar) was given non-null items and that the resulting object has an "accum" list with at least one element.
- **Downstream node reports a type or schema error**: The head output inherits its type from the accumulated items. Confirm that the items you accumulate are of the type the downstream node expects (for example, text vs. image), and adjust the accumulation pipeline or choose a compatible downstream node.
- **Passing a plain list does not work**: This node does not accept a bare array like ["a", "b"]. Wrap your list using List to Accumulation or build it via Accumulate so the structure is {"accum": [...]}, then pass that into this node.
- **Unexpected type when mixing items**: If you accumulate mixed types (e.g., first an image reference, then text), the head element’s type may not match what later steps assume. Keep each ACCUMULATION homogeneous in type, or add explicit branching or type-checking logic after this node.
