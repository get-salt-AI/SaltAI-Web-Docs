# Simple If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltIfNode evaluates a boolean condition and, for each connected value input, emits paired outputs representing the true and false branches. Each value_N input produces value_N_true and value_N_false outputs, with only the branch matching the condition carrying the original value and the other being null. It is designed for dynamic, multi-value conditional routing in Salt workflows and underpins higher-level constructs like loop controllers and conditional branch merges.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltIfNode when you need to split execution based on a boolean condition and route one or more values into separate true and false paths. Typical scenarios include deciding between a primary and fallback response, branching on confidence thresholds (for example high-confidence vs low-confidence handling), gating expensive operations behind a condition, or forming the conditional core of loop patterns. In a pipeline, place SaltIfNode after nodes that produce both the condition (BOOLEAN) and the payloads (value_1, value_2, etc.). Connect value_N_true outputs to the sequence of nodes that should run when the condition is true, and value_N_false outputs to those that should run when it is false. To recombine these branches later into a single downstream path, use a merge node such as SaltConditionalMerge (display name: "Merge If Branches") and connect the terminal outputs from the true and false chains into its branch inputs. When building loops or cyclic patterns, SaltIfNode can be combined with loop-oriented nodes such as SaltIfNodeSimpleLoop ("Do While Loop") or with explicit feedback edges where true-path outputs feed back into earlier steps. As a best practice, keep the meaning of each value_N input stable and documented so later changes do not misroute data, and always have upstream logic produce a clean BOOLEAN for the condition.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls which branch is active. If true, each value_N_true output carries the corresponding input value and value_N_false is null; if false, the opposite occurs. Accepts a boolean; strings like "true" or "false" are coerced, and other types are converted using generic truthiness rules.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First dynamic value to route based on the condition. Can be any Salt-supported type (text, numbers, objects, model outputs, etc.). Once this is connected, an additional input value_2 becomes available.</td><td style="word-wrap: break-word;">"How do I reset my account password?"</td></tr>
<tr><td style="word-wrap: break-word;">value_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second dynamic value to route with the same condition. Appears only after value_1 is connected. Use it for related data that should follow the same branch decision.</td><td style="word-wrap: break-word;">{"answer":"You can reset your password via the settings page.","confidence":0.84}</td></tr>
<tr><td style="word-wrap: break-word;">value_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third dynamic value to route. Additional value_N inputs continue to appear incrementally as the previous one is wired, up to the internal maximum input count.</td><td style="word-wrap: break-word;">[{"doc_id":"kb-102","score":0.92},{"doc_id":"kb-215","score":0.88}]</td></tr>
<tr><td style="word-wrap: break-word;">value_4+</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Represents all further dynamic inputs (value_4, value_5, and so on) that appear sequentially as you connect earlier inputs, up to the node’s maximum. Each one will be split into true/false outputs using the same condition.</td><td style="word-wrap: break-word;">{"aggregated_context":{"topics":["login","security"],"priority":"high"}}</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_1 when condition is true; otherwise null. This is the start of the true branch for the first payload.</td><td style="word-wrap: break-word;">"How do I reset my account password?" when condition is true; null when condition is false.</td></tr>
<tr><td style="word-wrap: break-word;">value_1_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_1 when condition is false; otherwise null. This is the start of the false branch for the first payload.</td><td style="word-wrap: break-word;">null when condition is true; "How do I reset my account password?" when condition is false.</td></tr>
<tr><td style="word-wrap: break-word;">value_2_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_2 when condition is true; otherwise null. Use this to route the second payload (for example, an LLM answer object) down the true branch.</td><td style="word-wrap: break-word;">{"answer":"You can reset your password via the settings page.","confidence":0.84} when condition is true.</td></tr>
<tr><td style="word-wrap: break-word;">value_2_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_2 when condition is false; otherwise null. Use this as the counterpart on the false branch.</td><td style="word-wrap: break-word;">null when condition is true; the same answer object when condition is false.</td></tr>
<tr><td style="word-wrap: break-word;">value_3_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_3 when condition is true; otherwise null. Continues the same true-branch pattern for additional values.</td><td style="word-wrap: break-word;">[{"doc_id":"kb-102","score":0.92},{"doc_id":"kb-215","score":0.88}] when condition is true.</td></tr>
<tr><td style="word-wrap: break-word;">value_3_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Carries value_3 when condition is false; otherwise null. Continues the false-branch pattern for additional values.</td><td style="word-wrap: break-word;">null when condition is true; the retrieval results list when condition is false.</td></tr>
<tr><td style="word-wrap: break-word;">value_N_true_false_dynamic</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">For every connected value_N input, the node dynamically creates a pair of outputs named value_N_true and value_N_false. Exactly one of the pair contains the original input value depending on the condition, and the other is null. The engine determines how many such pairs to expose based on how many value_N inputs are wired.</td><td style="word-wrap: break-word;">For N = 4, value_4_true = {"aggregated_context":{...}}, value_4_false = null when condition is true.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node inspects the workflow configuration at runtime to determine how many value_N inputs are connected and configures its outputs accordingly. This is normally lightweight, but many such nodes in a very large workflow can add modest planning overhead.
- **Limitations**: There is a fixed upper limit on the number of dynamic value inputs (for example, 100). Once this limit is reached, new value_N slots will not appear, so if you need to route many items, consider grouping them into a collection instead of separate inputs.
- **Behavior**: If no value_N inputs are connected, the node still has a minimal output structure, but you effectively get only nulls. In practice you should always connect at least one value to make the conditional branch meaningful.
- **Behavior**: Non-boolean condition inputs are coerced: strings are compared case-insensitively to "true", and other types use generic truthiness rules. This can lead to surprises if, for example, the string "False" is treated as true; prefer passing a proper BOOLEAN from upstream logic.
- **Behavior**: All outputs are non-list scalar values from the engine’s perspective; the node does not aggregate multiple inputs into lists. Each output is the single underlying value or null on the inactive branch.

## Troubleshooting
- **Both branches appear active**: If downstream nodes seem to run on both branches, check how they handle null. SaltIfNode always outputs one non-null and one null value per value_N pair; if a downstream node ignores nulls or treats them as valid, it may look like both branches executed.
- **Condition never evaluates false**: When condition originates from strings or numbers, values like "False" (string) or 1 may still coerce to true. Ensure the upstream node outputs a BOOLEAN type rather than textual or numeric representations of truth values.
- **Missing expected outputs**: If outputs like value_2_true or value_3_false are not visible, confirm that value_1, value_2, etc. are actually connected. New inputs and their corresponding outputs only become available after the previous input is wired.
- **Merge node always returns null**: When used with SaltConditionalMerge, a null merged_result usually indicates that both branch inputs to the merge are null. Verify that you connected the correct value_N_true or value_N_false outputs into the merge inputs and that the condition at this point in the workflow matches your expectations.
