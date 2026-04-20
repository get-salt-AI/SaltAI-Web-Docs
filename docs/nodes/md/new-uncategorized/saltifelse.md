# If / Else

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The If / Else node routes data between two optional inputs according to a boolean condition. When the condition is true, it outputs the on_true input; when it is false, it outputs the on_false input. The node uses lazy evaluation for the optional inputs, so only the branch that is actually selected is computed, which helps avoid unnecessary work on the unused branch.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltifelse.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to choose between two alternative values or processing paths based on a yes-or-no decision. Typical scenarios include toggling high versus low quality modes, enabling or bypassing an expensive operation, or switching between two different prompts or parameter sets. Place it mid-workflow: upstream nodes compute the BOOLEAN condition and prepare candidate outputs for on_true and on_false, while downstream nodes consume the single result output. It works well with logic and comparison nodes that produce boolean outputs, and with any nodes that generate images, text, models, or configuration objects to feed into the wildcard inputs. For reliable pipelines, ensure both branches produce the same kind of data so downstream nodes see a consistent type, and move heavy computations into the branches so they benefit from lazy evaluation.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean flag that decides which branch is selected. If condition is true, the node outputs the value from on_true; if condition is false, it outputs the value from on_false. This must be a proper boolean value, typically produced by an upstream comparison, logical operation, or a manual toggle node.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">on_true</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when condition is true. This input is lazy, so when condition is false, the upstream nodes connected here are not executed. It can carry any supported data type, such as an image, text, numeric parameter, model handle, or configuration object, as long as it matches what downstream nodes expect from result.</td><td style="word-wrap: break-word;">An image that has been processed with a high-detail enhancement model, used when a high_quality flag is true.</td></tr>
<tr><td style="word-wrap: break-word;">on_false</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when condition is false. This input is also lazy and is only computed when condition is false. It can carry any supported data type and should typically match the type produced by on_true so that downstream nodes receive a consistent kind of data.</td><td style="word-wrap: break-word;">The original, non-enhanced image used when high_quality is false to save processing time.</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value from either on_true or on_false, depending on the boolean condition. The type is whatever the chosen branch produces, such as an image, text block, numeric value, configuration object, or model reference. Downstream nodes must be able to accept this type regardless of which branch is active.</td><td style="word-wrap: break-word;">If condition is true, result may be a 1024x1024 upscaled image; if condition is false, result may be the original 512x512 image.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Only the selected branch is evaluated because the on_true and on_false inputs are lazy, which can greatly reduce runtime and resource usage when heavy operations are placed inside these branches.
- **Limitations**: If the branch chosen by condition is not connected or its upstream node produces no value, the node will output a null-like value, which can cause errors in downstream nodes expecting valid data.
- **Behavior**: The data type of result is determined entirely by the selected branch. If on_true and on_false produce different types, downstream nodes may fail or behave unpredictably unless they are designed to handle both types.
- **Behavior**: The condition input must be a BOOLEAN. Non-boolean values should be converted upstream; relying on implicit truthiness of other types can lead to incorrect routing or validation issues.

## Troubleshooting
- **Only one branch seems to run**: Symptom: The same branch is always used and the other never executes. Cause: The condition is constant, such as always true or always false. Fix: Inspect and test the upstream logic that sets condition, and temporarily replace it with a manual boolean toggle to verify that both branches work.
- **Downstream type mismatch errors**: Symptom: A downstream node reports an unexpected or unsupported input type. Cause: on_true and on_false output incompatible data types for the next node. Fix: Ensure both branches produce the same or compatible types, or restructure the workflow so each branch feeds different downstream nodes tailored to its type.
- **Null or missing output**: Symptom: result is empty or downstream nodes complain about missing required input. Cause: The active branch input is unwired or its upstream node did not produce any value. Fix: Confirm that any branch that might be selected is connected and that its upstream nodes are configured to emit a value in all relevant cases.
- **No performance gain from laziness**: Symptom: The workflow remains slow even though one branch should be skipped. Cause: Heavy computation is happening before this node instead of within the lazy branches. Fix: Move expensive processing steps into the nodes feeding on_true and on_false so that only the branch selected by condition is executed.
