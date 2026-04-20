# Lazy Conditional

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lazy Conditional lets you define up to 10 (condition → value) pairs plus an else fallback and returns the value for the first condition that evaluates to true. Only the branch that is actually selected is evaluated, thanks to lazy inputs, which avoids running unnecessary or expensive parts of your pipeline. Because it uses a wildcard type, it can route images, text, prompts, models, or other data structures transparently.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltlazyconditional.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need more than a simple if/else and want to pick between multiple alternative branches based on boolean flags. Typical scenarios: selecting different prompts or styles based on user segment, choosing a lighter model when GPU memory is low, or routing to different post-processing chains depending on request metadata. Place it after nodes that compute BOOLEAN conditions (for example, comparison or feature-flag nodes) and before the part of your graph that consumes the chosen value (such as samplers, model loaders, or text processors). Related logic nodes are "Lazy Switch" (SaltLazySwitch) for a single if/else, and "Lazy Index Switch" (SaltLazyIndexSwitch) when you want to choose by integer index rather than many booleans. A practical pattern is: gather all candidate values (value1…valueN) and their conditions (condition1…conditionN), wire a safe default into else, and then feed the output into downstream processing. Put the most common or most critical conditions earlier (condition1, condition2, etc.) so they short-circuit quickly and keep later branches as fallbacks.

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
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value returned when condition1 is true. Marked lazy so it is only computed if condition1 becomes the selected branch. Can be any supported type (image tensor, prompt object, model reference, configuration dict, and so on), but should match what downstream nodes expect.</td><td style="word-wrap: break-word;">A high-quality image generation configuration used when condition1 = "user_is_premium" is true.</td></tr>
<tr><td style="word-wrap: break-word;">condition1</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean condition checked. If true, value1 is selected and all later conditions are ignored. Must be provided from another node (no built-in default).</td><td style="word-wrap: break-word;">true if metadata.tier == "premium", otherwise false.</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value returned when condition2 is true and condition1 is false. Also lazily evaluated and can be any wildcard-compatible type.</td><td style="word-wrap: break-word;">A faster, cheaper model configuration used when condition2 = "is_rush_order" is true.</td></tr>
<tr><td style="word-wrap: break-word;">condition2</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second condition in the chain, evaluated only if condition1 is false. If true, selects value2.</td><td style="word-wrap: break-word;">true if request.flags.contains("rush"), otherwise false.</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third candidate value, used when condition3 is the first true condition after earlier ones fail.</td><td style="word-wrap: break-word;">A low-resolution preview image pipeline configuration when preview mode is requested.</td></tr>
<tr><td style="word-wrap: break-word;">condition3</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Third boolean condition. Only relevant if you wire value3 and need a third branch.</td><td style="word-wrap: break-word;">true if settings.mode == "preview".</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth candidate value. Use this and later valueN inputs for more specialized routing cases.</td><td style="word-wrap: break-word;">A style embedding for illustration-style outputs when style == "illustration".</td></tr>
<tr><td style="word-wrap: break-word;">condition4</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fourth condition, guarding value4. Only checked if conditions 1–3 are false.</td><td style="word-wrap: break-word;">true if metadata.style == "illustration".</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth candidate value, still evaluated lazily. Useful for layered fallbacks.</td><td style="word-wrap: break-word;">A diagnostic report configuration used in debug mode.</td></tr>
<tr><td style="word-wrap: break-word;">condition5</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Fifth condition, checked after 1–4. Can be used for debug or diagnostic branches.</td><td style="word-wrap: break-word;">true if debug_mode == true.</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Sixth candidate value, e.g., for hardware-constrained routes.</td><td style="word-wrap: break-word;">A lightweight model handle used on low-VRAM machines.</td></tr>
<tr><td style="word-wrap: break-word;">condition6</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Sixth condition; if true and earlier ones are false, selects value6.</td><td style="word-wrap: break-word;">true if available_vram_mb < 6000.</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Seventh candidate value for additional specialization.</td><td style="word-wrap: break-word;">A mobile-optimized layout or prompt when client platform is mobile.</td></tr>
<tr><td style="word-wrap: break-word;">condition7</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Seventh condition controlling value7.</td><td style="word-wrap: break-word;">true if client_platform == "mobile".</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Eighth candidate, often used for policy- or visibility-based routing.</td><td style="word-wrap: break-word;">An output preset with watermarking enabled for public images.</td></tr>
<tr><td style="word-wrap: break-word;">condition8</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Eighth condition, only reached if 1–7 are false.</td><td style="word-wrap: break-word;">true if visibility == "public".</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Ninth candidate value, typically for cost-sensitive routes.</td><td style="word-wrap: break-word;">A cheaper inference backend identifier for high cost-sensitivity requests.</td></tr>
<tr><td style="word-wrap: break-word;">condition9</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Ninth condition in the chain.</td><td style="word-wrap: break-word;">true if cost_profile == "budget".</td></tr>
<tr><td style="word-wrap: break-word;">value10</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Tenth and final explicit candidate value before else is used.</td><td style="word-wrap: break-word;">A fully offline workflow configuration for air-gapped deployments.</td></tr>
<tr><td style="word-wrap: break-word;">condition10</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Tenth and final condition; if true (and 1–9 are false) selects value10.</td><td style="word-wrap: break-word;">true if deployment_mode == "offline".</td></tr>
<tr><td style="word-wrap: break-word;">else</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fallback value returned when none of the conditions 1–10 are true. Also lazily evaluated. Strongly recommended to connect this to avoid null outputs.</td><td style="word-wrap: break-word;">A default model and prompt configuration used when no feature flags or special conditions match.</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value from the first true condition's valueN input, or the else value if all conditions are false. Its type matches the chosen input branch, so downstream nodes must be able to handle whatever types you may route through here.</td><td style="word-wrap: break-word;">If condition2 ("is_rush_order") is the first true condition, the output is the rush-optimized prompt object from value2; if no conditions are true, the output is the generic configuration wired to else.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Only the value corresponding to the first true condition (or else) is computed because value inputs are lazy, which can significantly reduce GPU and CPU load when branches are expensive.
- **Limitations**: Evaluation order is strictly fixed from condition1 through condition10; there is no dynamic reordering or weighting, so earlier conditions always take precedence over later ones.
- **Behavior**: If you wire a conditionN without wiring the corresponding valueN, the node may request that value lazily and effectively behave as if the branch is incomplete; always connect both sides of any branch you intend to use.
- **Behavior**: If all conditions are false and no else input is provided, the node returns a null-equivalent, which can cause downstream nodes that expect a concrete image, text, or configuration to fail.
- **Compatibility**: Ensure that all valueN and else inputs are of types that downstream nodes can accept; mixing incompatible types across branches (for example, some images and some plain strings) can lead to runtime errors later in the pipeline.

## Troubleshooting
- **Branch Never Selected**: If a branch you expect to trigger does not, verify that its conditionN is actually true at runtime and that no earlier condition is also true, because the first true condition always wins.
- **Null or Missing Output**: If downstream nodes complain about receiving null or missing data, check that either at least one condition can be true for your scenario or that you have connected an else value as a fallback.
- **Type Mismatch Downstream**: If only some requests cause type-related errors, inspect which branch is selected in those cases and confirm that its valueN type matches what the downstream node expects.
- **Misaligned Indexing**: If behavior seems random or wrong, confirm you have not inadvertently connected condition3 to value4 or similar; indexes must match (conditionN with valueN) for each branch to behave as intended.
