# Debug Print

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltDebugPrint logs a human-readable summary of any incoming value together with a user-provided label. It understands common container types and tensors, summarizing their structure (for example, tensor shapes) for concise console output. The original value is forwarded unchanged, so you can drop this node anywhere in a workflow without altering behavior.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltdebugprint.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltDebugPrint when you need visibility into what data is flowing through a specific point in your workflow. Common scenarios include verifying that an upstream node produces the expected structure, inspecting tensor shapes in model or image pipelines, and checking nested lists, tuples, or dicts during development. Place the node inline: connect any node’s output to the value input, then connect SaltDebugPrint’s value output to the next processing node. It is especially useful between loading/preprocessing and model inference steps, or between major transformation stages such as SaltImageLoader → SaltDebugPrint → a model or transform node. Because it lives under SALT/Utility/Debug, it is intended as a temporary diagnostic tool—add it while debugging, then remove it once your workflow is stable. For best results, give each instance a clear, unique label (for example, "encoder_output_step_3") so you can easily identify each log line in shared or complex workflows.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to inspect and log. This can be any type, including strings, numbers, lists, tuples, dicts, and tensors. Lists, tuples, and dicts are traversed recursively and summarized; tensors are reported by their shape such as Tensor[1, 3, 512, 512]. Extremely large or deeply nested values may generate long log messages.</td><td style="word-wrap: break-word;">[{"prompt": "a cinematic landscape at sunset", "steps": 30}, {"prompt": "studio portrait with soft lighting", "steps": 20}]</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short label used to tag this debug output in the logs. It appears as a prefix around the value representation, helping you distinguish multiple SaltDebugPrint nodes and identify where in the workflow the message originated.</td><td style="word-wrap: break-word;">post_encoder_latent_tensor_batch0</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The exact same object provided on the value input, returned without modification. Downstream nodes can consume this as if SaltDebugPrint were not present, which makes it safe to insert or remove this node purely for debugging.</td><td style="word-wrap: break-word;">Tensor[1, 4, 64, 64] (at runtime this is the same tensor object that entered value; only its shape is logged, not its contents).</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Excessive logging of very large or deeply nested structures, or frequent logging inside iterative sections, can slow execution and produce very large log files. Use this node sparingly in performance-critical paths.
- **Limitations**: Tensors are summarized only by their shape, not by individual values. Complex or custom objects that are not standard containers or tensors are typically represented by their type name, which may limit how much detail you see.
- **Behavior**: SaltDebugPrint never mutates the incoming value. It builds a debug string, sends it to the internal logger along with the label, and returns the original object reference so downstream computations remain unchanged.
- **Behavior**: If an error occurs while building the debug representation, the node logs an error message and falls back to a simple string conversion of the value, but it still returns the original value so the workflow continues to run.

## Troubleshooting
- **No debug messages visible**: Confirm that the logging configuration allows debug-level messages. This node uses debug severity; if the global log level is set to info or higher, its output will not appear.
- **Logs are too verbose or cluttered**: You may be printing very large or frequent values. Reduce the number of SaltDebugPrint nodes, move them to less frequently executed branches, or temporarily raise the global log level above debug to hide these messages.
- **Only a type name appears in the log**: When the log shows something like MyCustomClass instead of a detailed structure, it means the node could not derive a richer representation and fell back to the type name. If you need more detail, convert such objects into simpler structures (for example, dicts or lists of primitives) upstream.
- **Workflow feels slower while debugging**: Heavy debug logging, especially in loops or high-throughput sections, can add noticeable overhead. Remove or disable SaltDebugPrint nodes once you have validated the data at those points.
