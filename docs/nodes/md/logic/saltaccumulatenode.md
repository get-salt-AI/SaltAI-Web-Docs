# Accumulate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Accumulate collects incoming values over time into a single ACCUMULATION structure. Each call appends the new value to the existing accumulation while safely handling missing or None inputs. This node is the primary way to iteratively build lists in Salt workflows using the ACCUMULATION type.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Accumulate node whenever you need to progressively build a list of values across steps, loops, or branches in a workflow. Typical scenarios include collecting generated texts, images, embeddings, or intermediate results to process together later. Upstream, the to_add input is usually driven by nodes such as text generation nodes, model inference nodes, or computed scalars, while the optional accumulation input is commonly fed from a previous Accumulate call or from List to Accumulation. Downstream, the resulting ACCUMULATION is often consumed by nodes like Accumulation Head, Accumulation Tail, Accumulation Get Item, Accumulation Get Length, or Accumulation to List for indexing, iteration, or batch processing. Best practices: (1) Always pass the previous accumulation output back into the next Accumulate call if you want to keep growing the same sequence. (2) Use it in combination with loop or branching patterns where the same ACCUMULATION is threaded through multiple steps. (3) Rely on its built-in handling of None inputs when some iterations may not produce a value; these are simply skipped rather than polluting your list.

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
<tr><td style="word-wrap: break-word;">to_add</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to append to the accumulation on this execution. Accepts any supported type (text, number, image, embedding, object, etc.). If this is None, the node will not add a new item and will just return the existing accumulation (or an empty one when no prior accumulation exists).</td><td style="word-wrap: break-word;">A generated product description string such as "Lightweight waterproof hiking backpack, 30L capacity."</td></tr>
<tr><td style="word-wrap: break-word;">accumulation</td><td>False</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The existing ACCUMULATION to extend. Usually connected from the accumulation output of a previous Accumulate node or created via List to Accumulation. If omitted or None, the node starts a new accumulation. If a non-ACCUMULATION value is supplied, it will be wrapped together with the new value into a fresh ACCUMULATION.</td><td style="word-wrap: break-word;">{ "accum": ["First summary paragraph.", "Second summary paragraph."] }</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An ACCUMULATION structure wrapping the collected sequence of values under the accum key. The internal order corresponds to the order in which to_add values were provided across executions. This is designed to be passed into downstream list-manipulation nodes or fed back into another Accumulate call.</td><td style="word-wrap: break-word;">{ "accum": ["User query 1", "User query 2", "User query 3"] }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The ACCUMULATION grows linearly; very large accumulations can increase memory usage and slow down downstream operations that traverse the entire list.
- **Limitations**: The node stores values in-memory within the workflow execution; it is not a persistent store and will not survive across separate runs or deployments.
- **Behavior**: When to_add is None, the node returns the existing accumulation unchanged (or an empty one if none exists), which is useful when some branches or iterations yield no result.
- **Behavior**: If accumulation is not in the expected ACCUMULATION shape, the node gracefully wraps existing and new values into a valid { "accum": [...] } structure instead of failing.

## Troubleshooting
- **Result list is unexpectedly empty**: This often occurs when you never feed the previous accumulation output back into the next Accumulate node, causing each call to start fresh. Ensure the accumulation input is wired from the prior Accumulate step.
- **Values are missing from the accumulation**: Check whether some iterations produce None for to_add. The node intentionally skips None values; add guards or defaults upstream if you need placeholders to appear in the sequence.
- **Type or shape mismatches downstream**: Some list-processing nodes expect a proper ACCUMULATION object. If you manually constructed or transformed the input, confirm it has the form { "accum": [...] } before passing it to Accumulation Head, Accumulation Tail, or similar nodes.
- **Unexpected single-item accumulation**: If you pass a raw non-ACCUMULATION value into the accumulation input, it is treated as the first element and combined with to_add. If you only want to accumulate the to_add values, ensure accumulation is either empty, a valid ACCUMULATION, or not provided on the first call.
