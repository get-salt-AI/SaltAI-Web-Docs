# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node aggregates up to 50 separate BOLTZ inputs (such as sequences, constraints, templates, or properties) into one combined list. Each input can be a single BOLTZ object or an existing list of BOLTZ objects; list inputs are flattened so the output is always a single-level list. The node preserves the numeric input order, enabling reproducible, ordered BOLTZ configurations built from multiple sources.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to unify several BOLTZ components into a single list before sending them to nodes that operate on complete BOLTZ configurations or batches. Typical scenarios include merging multiple binder sequences with different structural constraints, combining templates and properties with one or more sequences, or consolidating BOLTZ lists produced by parallel branches in your workflow. Place it mid-pipeline: upstream, connect nodes that emit BOLTZ-compatible objects (for example nodes that define binder sequences, distance constraints, templates, or affinity properties); downstream, connect nodes that expect a single list of BOLTZ entities, such as BOLTZ job submission, multi-component prediction, or scoring/analysis nodes. The node reveals additional inputs incrementally—input_2 appears after input_1 is connected, input_3 after input_2, and so on—keeping the UI cleaner while still supporting up to 50 inputs. When you already have lists from upstream nodes, pass them directly; this node will handle flattening, which helps keep your graph simple.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First BOLTZ input and the only mandatory one. Accepts either a single BOLTZ object (for example a sequence, constraint, template, or property) or a list of BOLTZ objects. If a list is provided, each element of that list is appended individually to the final combined_list.</td><td style="word-wrap: break-word;">[{"sequence": "EVQLVESGGGLVQPGGSLRLSCAASGFTFSSYAMSWVRQAPGKGLE"}]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second optional BOLTZ input. Only becomes available after input_1 is connected. Can be a single BOLTZ object or a list of BOLTZ objects; list elements are flattened into the combined_list.</td><td style="word-wrap: break-word;">{"constraint": {"type": "distance", "residue_i": 35, "residue_j": 92, "max_distance": 8.0}}</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third optional BOLTZ input, made visible after input_2 is connected. Also accepts either a single BOLTZ object or a list; if a list is given, each element is expanded into a separate entry in combined_list.</td><td style="word-wrap: break-word;">[{"template_pdb_id": "6M0J", "chain_id": "A"}, {"template_pdb_id": "7K8S", "chain_id": "B"}]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single flattened list containing all BOLTZ objects from every connected input from input_1 through input_50. If an input was a list, each of its elements is added individually; if an input was a single object, that object becomes one element in the list. This output is suitable for downstream BOLTZ or AlphaFold-related nodes that expect a unified list of configuration objects or entities.</td><td style="word-wrap: break-word;">[{"sequence": "EVQLVESGGGLVQPGGSLRLSCAASGFTFSSYAMSWVRQAPGKGLE"}, {"constraint": {"type": "distance", "residue_i": 35, "residue_j": 92, "max_distance": 8.0}}, {"template_pdb_id": "6M0J", "chain_id": "A"}, {"affinity": {"binder": "BOLTZ_CHAIN_1234"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node iterates over up to 50 inputs and flattens lists into a single list; this is lightweight for typical BOLTZ workloads, but extremely large lists (thousands of elements) can increase memory usage and add overhead to subsequent nodes.
- **Limitations**: At least one non-empty input must be provided; if no valid inputs are connected, the node raises an error stating that at least one input is required. Inputs that are missing or None are simply skipped.
- **Behavior**: List inputs are flattened; nested lists are not preserved. Passing a list into this node merges its contents into the top-level combined_list rather than keeping a list-of-lists structure.
- **Behavior**: Inputs are processed in numeric order from input_1 to input_50, so the order of wiring and assignment directly determines the order of elements in combined_list, which can matter for downstream nodes with positional semantics.
- **Behavior**: Additional inputs (input_2 and higher) remain hidden until the previous input is connected, which keeps the UI compact but requires sequential connection if you want to expose many inputs.

## Troubleshooting
- **Common Error 1**: Error message "At least one input is required" – This occurs when no input_i fields provide usable data. Ensure that at least input_1 is connected to a valid BOLTZ object or a non-empty list.
- **Common Error 2**: Downstream node reports unexpected data shape – Because this node flattens list inputs, a downstream node expecting a list-of-lists may instead receive a flat list. To fix this, avoid passing that nested list through the combiner, or adjust the downstream node to work with a flat list.
- **Common Error 3**: Later inputs (for example, input_5) do not appear – Inputs beyond the last connected one are hidden. Connect input_1, then input_2, and so forth in sequence to reveal additional input ports.
- **Common Error 4**: Mixed object types cause downstream failures – If you combine sequences, constraints, templates, and properties in a single combined_list but a consumer node expects only one type (for example, sequences only), it may fail. Resolve this by separating pipelines by type or ensuring the downstream node supports heterogeneous lists.
