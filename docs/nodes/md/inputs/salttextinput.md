# Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltTextInput is the primary entrypoint node for user text in Salt workflows. It presents a text field (single- or multi-line) in the UI, captures what the user types, and outputs that text as a string for downstream processing. It also emits a small UI payload so the platform can track the latest submitted value for this node instance.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/salttextinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltTextInput whenever you need human-entered text to drive your workflow, such as prompts for language models, search queries, configuration parameters, or free-form feedback. It typically appears near the start of a workflow, upstream of processing nodes like model invocation, text analyzers, or routing/decision nodes. A common pattern is SaltTextInput → prompt-building or formatting node → model invocation node → SaltOutput or similar display/response node. You can connect its output to any node that expects a STRING input. For multi-input workflows, multiple SaltTextInput nodes can collect different fields (e.g., “User Question”, “Context Notes”, “Formatting Instructions”). Always ensure your client or orchestration layer calls `setNodeInputValues([{ nodeId, inputName: "input", value }])` on this node, since its `input` is required even when empty; failing to do so will cause execution-time validation errors. Pair it with SaltOutput to display final responses, or with logic nodes that branch behavior based on the entered text (e.g., keyword routing, command parsing, or template substitution).

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text value entered by the user that will be passed through the workflow. Supports both single-line and multi-line content. This field is required: the orchestration layer must explicitly provide a string, which may be empty ("") but cannot be omitted. There are no strict length limits at the node level, but extremely long strings may impact downstream model performance or token limits.</td><td style="word-wrap: break-word;">Summarize the following article in 3 bullet points focusing on key business impacts.</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The exact text the user entered, normalized so that `None` becomes an empty string. Downstream nodes consume this value as their input text, for example as the prompt to a language model or the body of a query. The output is a single scalar string, not a list.</td><td style="word-wrap: break-word;">Compare the security features of Product A and Product B and recommend which is better for a healthcare startup.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight; most performance considerations come from how large the input text is for downstream models or services. Very long text inputs can lead to slow or expensive model calls later in the workflow.
- **Limitations**: This node does not validate or transform the text beyond ensuring a string value exists. Any constraints (e.g., maximum length, required keywords, or formatting) must be handled by downstream logic or UI-side validation.
- **Behavior**: If the provided `input` is `null`/`None`, the node coerces it to an empty string ("") rather than failing. However, omitting the `input` field entirely at execution time is treated as a missing required input and will cause an error.
- **Behavior**: The node is marked as an output node in the UI sense (it surfaces a value originating from the user), but logically it functions as an entrypoint for data entering the workflow graph. Its internal UI payload includes the node’s `unique_id`, last `output` value, and a timestamp placeholder used by the platform.

## Troubleshooting
- **Required input is missing: input**: This occurs when the workflow is executed without explicitly setting the node's `input` value. Ensure your client code calls `setNodeInputValues([{ nodeId: '<id>', inputName: 'input', value: userText ?? '' }])` for every SaltTextInput node before running the workflow.
- **Unexpected null/undefined text downstream**: If downstream nodes complain about null or undefined text, verify that you are passing a proper string to `input` rather than leaving it undefined. The node will coerce `null` to "", but undefined/missing fields will fail validation.
- **UI not reflecting latest entered text**: If the workflow runs but the UI appears out of sync, confirm that the same `unique_id` is being used consistently and that you are not reusing node IDs across different workflows or sessions without reinitializing.
- **Downstream token limit or size errors**: If a model node errors due to token or size limits, the issue is usually the length of the text captured by SaltTextInput. Add an intermediate node to truncate, summarize, or validate the input length before passing it to token-constrained services.
