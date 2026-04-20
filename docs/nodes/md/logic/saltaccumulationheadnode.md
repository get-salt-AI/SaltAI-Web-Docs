# Accumulation Head

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node extracts the first item from an accumulation structure produced by Salt list/logic nodes. It expects an ACCUMULATION object (a wrapper around a list stored under an "accum" key) and returns the head element as a generic value. If the accumulation is empty or malformed, it safely returns null instead of failing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationheadnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you are building iterative or list-processing workflows and need to read the first element collected so far. A common pattern is to build up an ACCUMULATION using "Accumulate", then branch or inspect its contents using "Accumulation Head" (to read the first item) and "Accumulation Tail" (to get the remaining items). Upstream, "Accumulate" or "List to Accumulation" typically provide the "accumulation" input; downstream, any node that accepts the same data type as you originally accumulated (text, image, number, etc.) can consume the "head" output. This is useful for workflows like taking the first generated candidate from a batch, inspecting the first error message collected during validation, or peeking at the first image in a set. Always design downstream logic to handle the case where `head` is null (for example, when nothing has been accumulated yet), and ensure you pass a valid ACCUMULATION object produced by official accumulation nodes rather than hand-crafted dictionaries.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object wrapping a list of previously collected values. Internally this is a structured object (a dictionary-like value) containing an "accum" list, created by nodes such as Accumulate or List to Accumulation. It should contain zero or more items of a single, consistent type (for example, all text prompts, all images, or all numeric scores). If the "accum" list is empty or the object is not a valid accumulation, the node will return null as the head.</td><td style="word-wrap: break-word;">{"accum": ["draft product description A", "draft product description B", "draft product description C"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">head</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The first element in the accumulation's internal list. The type matches whatever you originally accumulated (for example, text, image, or numeric value). If the accumulation is empty, missing its internal list, or otherwise invalid, this output will be null. Downstream nodes should be prepared to handle a null value when no head element is available.</td><td style="word-wrap: break-word;">"draft product description A"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node only reads the first element of the internal list, so it runs in constant time and adds negligible overhead even for large accumulations.
- **Limitations**: If you pass anything other than a proper ACCUMULATION object (for example, a raw list or an arbitrary dictionary without an "accum" key), the node does not throw an error but simply returns null as the head.
- **Behavior**: When the internal "accum" list is empty, the node returns null instead of raising an error; downstream logic should explicitly check for null before using the value.
- **Behavior**: The node is type-agnostic (WILDCARD), but you should keep all accumulated elements the same type to avoid confusion or type errors in downstream consumers.

## Troubleshooting
- **Common Error 1**: Head is always null. Cause: The input is not a valid ACCUMULATION or its internal "accum" list is empty. Fix: Ensure the accumulation comes from nodes like Accumulate or List to Accumulation and verify that items are actually being added.
- **Common Error 2**: Downstream node rejects the head value. Cause: Type mismatch (for example, text head fed into an image-only node). Fix: Confirm that the items you accumulate are of the type expected by the downstream node and adjust the upstream accumulation logic accordingly.
- **Common Error 3**: Head becomes unexpectedly null after several iterations. Cause: The accumulation is being reinitialized or overwritten instead of updated. Fix: Make sure each loop iteration passes the updated accumulation object forward and that you are not resetting it to an empty or null value.
