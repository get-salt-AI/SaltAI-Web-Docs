# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node aggregates up to 50 BOLTZ objects (such as sequences, constraints, templates, or properties) and BOLTZ lists into one combined list. It accepts both single objects and lists, flattening list inputs into a single sequence while preserving the order of inputs. This makes it easy to unify multiple BOLTZ components into a single structure for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to merge multiple BOLTZ-compatible items into a single list for further analysis or execution. Common scenarios include combining several binder sequences with constraint definitions and template objects into one batch for Boltz-related modeling or scoring, or consolidating lists created in parallel branches of a workflow. Place this node downstream of BOLTZ-producing nodes (for example, sequence generator nodes, constraint builders, template or property nodes) and upstream of nodes that expect a list of BOLTZ items (such as batch prediction or evaluation nodes in the Biotech/Boltz pipeline). Connect at least `input_1` with a valid BOLTZ object or list; then optionally connect `input_2` through `input_50` as needed. The node will iterate from `input_1` to `input_50`, flatten any list inputs and append single objects, returning one ordered list suitable for subsequent nodes.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The primary BOLTZ object or list of BOLTZ objects to include in the combined list. Must be a BOLTZ-compatible value (for example, a binder sequence object, a constraint object, a template object, a property object, or a list of such elements). At least one of the inputs (starting with this one) must be provided, or the node will error.</td><td style="word-wrap: break-word;">[{"sequence":"QVQLVESGGGLVQAGGSLRLSCAASGFTFSSYAMSWVRQAPGKGLEW","type":"binder_sequence"}]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional second BOLTZ object or list to append. If this input is provided as a list, its elements are extended into the combined list; if provided as a single object, it is appended as one element. Typically used to add constraints, additional sequences, or extra design items.</td><td style="word-wrap: break-word;">{"constraint":{"target":"CDR-H3","max_mutations":3},"type":"constraint"}</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional third BOLTZ object or list, following the same semantics as `input_2`. Use this and higher inputs to incrementally add more sequences, constraints, templates, or properties as the design grows.</td><td style="word-wrap: break-word;">[{"template_id":"alphafold_model_1","type":"template"},{"template_id":"alphafold_model_2","type":"template"}]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single list containing all non-null BOLTZ items gathered from `input_1` through `input_50`. For each input, if the value is a list, its elements are added individually; if it is a single object, it is added as a single element. The resulting list preserves the order of inputs and the order within each list, and is suitable for downstream nodes that expect a unified list of BOLTZ objects.</td><td style="word-wrap: break-word;">[{"sequence":"QVQLVESGGGLVQAGGSLRLSCAASGFTFSSYAMSWVRQAPGKGLEW","type":"binder_sequence"},{"constraint":{"target":"CDR-H3","max_mutations":3},"type":"constraint"},{"template_id":"alphafold_model_1","type":"template"},{"affinity":{"binder":"BINDER_CHAIN_1234"},"type":"property"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node concatenates lists in memory, so very large numbers of items across many inputs can increase memory usage and execution time. For extremely large batches, consider combining subsets in stages.
- **Limitations**: The node defines a fixed maximum of 50 inputs (`input_1` through `input_50`). If you need more distinct sources, first merge some upstream into intermediate lists before feeding them here.
- **Behavior**: If an input value is a list, its contents are flattened into the final `combined_list`. Non-list inputs are appended as single elements. This can change nesting compared to upstream structures, so design downstream expectations accordingly.
- **Behavior**: If no inputs contain usable data (for example, all are missing or null), the node raises an error instead of returning an empty list. At least one input must supply a BOLTZ object or list.
- **Behavior**: The order of the combined output strictly follows input index order (`input_1`, then `input_2`, etc.) and the internal order of items inside each list input, which can be important when downstream logic assumes a stable sequence.

## Troubleshooting
- **Common Error 1**: Error with message similar to "At least one input is required" – This means all inputs were missing or empty. Ensure that at least `input_1` is connected to a BOLTZ-producing node and that the upstream node successfully outputs data.
- **Common Error 2**: Downstream node reports an unexpected data shape – Because this node flattens list inputs, a consumer expecting nested lists may fail. In that case, either avoid flattening by not combining those lists here, or wrap items back into the needed structure in a downstream transformation node.
- **Common Issue 3**: Some elements appear missing in the output – Verify that the corresponding inputs are actually being passed to the node and are not null. Also confirm that the upstream nodes execute in the same run and produce the expected non-empty outputs.
- **Common Issue 4**: Output order does not match expectations – Remember that this node preserves the order of inputs and their internal list order. If a different ordering is needed (for example, grouping by type), add an ordering or sorting step downstream or reorder inputs upstream.
