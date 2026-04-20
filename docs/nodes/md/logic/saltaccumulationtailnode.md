# Accumulation Tail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltAccumulationTailNode takes an ACCUMULATION object and produces a new ACCUMULATION that contains every element except the first. If the input contains zero or one element, it returns an empty accumulation object. This enables safe, stepwise processing of list-like data where you repeatedly remove the head and work with the remaining tail.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationtailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to drop the first element of an accumulation while keeping the data in ACCUMULATION form for further list-logic processing. It fits naturally in workflows that iterate over collections: an upstream node such as SaltAccumulateNode or SaltListToAccumulationNode builds the ACCUMULATION, then SaltAccumulationHeadNode reads the current element while SaltAccumulationTailNode passes the rest onward. Downstream nodes often include another SaltAccumulationTailNode for further peeling, SaltAccumulationGetLengthNode for loop termination checks, or SaltAccumulationToListNode when you finally need a plain list. A typical pattern is: create accumulation from items, read head for processing (e.g., next message chunk or task), forward the tail to the next step, and repeat until the accumulation becomes empty.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The input accumulation object to trim. This must be a structure of the form {"accum": [...]}, where the value of 'accum' is a list of items of any supported type. If the list has length greater than 1, the node returns a new accumulation whose 'accum' list excludes the first element. If the list has length 0 or 1, the node returns an accumulation with an empty list. If the input is not a dict with an 'accum' key, it is treated as invalid and will result in an empty accumulation.</td><td style="word-wrap: break-word;">{"accum": ["session_step_1", "session_step_2", "session_step_3"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">tail</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An ACCUMULATION object containing all elements of the input accumulation after the first. Internally this is a dict with an 'accum' key pointing to the remaining list. If the input has zero or one element, the output will be {"accum": []}. This output can be consumed by other list-logic nodes like SaltAccumulationHeadNode, SaltAccumulationTailNode (for further peeling), SaltAccumulationGetLengthNode, or converted to a plain list with SaltAccumulationToListNode.</td><td style="word-wrap: break-word;">{"accum": ["session_step_2", "session_step_3"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node constructs the tail using a list slice of the underlying 'accum' list, which runs in O(n) time relative to the number of remaining elements. This is generally efficient but can add overhead if you repeatedly call it on very large accumulations in tight loops.
- **Limitations**: The node expects a dict with an 'accum' key whose value is a list. If a different structure is passed (for example, a raw list or a dict without 'accum'), the node will not raise an error but will effectively behave as if the accumulation is empty and return {"accum": []}.
- **Behavior**: When the input accumulation has length 0 or 1, the node always returns an accumulation with an empty 'accum' list instead of returning null or the original data. This makes iterative processing safer but can hide logical mistakes if your workflow assumes there are always at least two items.
- **Behavior**: On unexpected internal errors, the node logs the issue and returns an empty accumulation. If you see unexpectedly empty tails, inspect upstream nodes to confirm that they are producing a correctly structured ACCUMULATION.

## Troubleshooting
- **Common Error 1**: Tail appears empty even though items were added. Solution: Confirm that the upstream node is producing a proper ACCUMULATION object (e.g., via SaltAccumulateNode or SaltListToAccumulationNode) and not a plain list or another type. Also verify that the 'accum' list actually contains more than one element at the point where you call SaltAccumulationTailNode.
- **Common Error 2**: Workflow processes only the first element and then stops. Solution: If you are using the tail’s length to decide whether to continue, remember that once there is only one element left, the tail becomes empty. Adjust your loop logic to check the length of the original accumulation before cutting off the head and tail, or to stop when the current accumulation length is 0 instead of when the tail is empty.
- **Common Error 3**: Downstream nodes complain about input type or fail silently. Solution: Make sure you do not convert the accumulation to a plain list with SaltAccumulationToListNode before passing it into SaltAccumulationTailNode. This node strictly expects an ACCUMULATION, not a raw list; if given a list, it will treat it as invalid and output an empty accumulation.
