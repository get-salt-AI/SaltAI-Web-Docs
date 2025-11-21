# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug class information for a given RxNorm Concept Unique Identifier (RxCUI). It queries the RxClass service via the RxNorm API and returns a formatted JSON string containing the classes associated with the drug, optionally filtered by a classification system.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have an RxCUI for a drug and need to obtain its classification across selected systems (e.g., therapeutic classes). Typically, pair it with a node that resolves a drug name or NDC to an RxCUI, then feed that RxCUI here to fetch class memberships.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier representing the drug concept to classify.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Classification system filter. Options are provided by the platform (via RxNormAPI.drug_class_source_relations). Use 'ALL' to return classes from all supported systems.</td><td style="word-wrap: break-word;">ALL</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with the structure: { "rxcui": <input rxcui>, "classification_system": <input system>, "classes": <raw API results> }.</td><td style="word-wrap: break-word;">{   "rxcui": "83367",   "classification_system": "ALL",   "classes": { /* RxClass API payload */ } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input RxCUI is required; the node returns an error if it is empty.
- Classification system choices are constrained to the provided dropdown options; select 'ALL' to include all available systems.
- The 'drug_classes' output embeds the raw API response under the 'classes' key for full fidelity; downstream nodes may need to parse it.
- This node does not convert drug names or NDCs to RxCUIs. Use a separate node to resolve identifiers before calling this node.

## Troubleshooting
- If status returns 'Error: RXCUI cannot be empty', ensure you provide a non-empty RxCUI string.
- If status indicates 'API Error: ...', verify network connectivity and that the RxCUI is valid; retry later if the external API is unavailable.
- If 'classes' is empty but the RxCUI is valid, try setting classification_system to 'ALL' to broaden results.
- If you see unexpected results, confirm the RxCUI corresponds to the intended concept (consider resolving it again from the source drug name).
