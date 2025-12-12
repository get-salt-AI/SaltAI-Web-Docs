# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug class classifications for a given RxNorm Concept Unique Identifier (RXCUI). It returns a JSON string summarizing the classes associated with the drug and a status message. You can optionally filter by a specific classification system (e.g., ATC, MeSH, VA).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RXCUI and need to look up its drug classes. Commonly, you first resolve a drug name or NDC to an RXCUI using a separate node, then feed that RXCUI into this node to obtain its classifications for downstream analysis, filtering, or display.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier of the drug to classify.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">MULTIPLE_CHOICE</td><td style="word-wrap: break-word;">Optional filter for the classification source/system. Options are fetched dynamically from RxClass (e.g., ATC, MeSH, VA).</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the RXCUI, selected classification system, and the list of classes returned by the service.</td><td style="word-wrap: break-word;">{   "rxcui": "83367",   "classification_system": "ATC",   "classes": { "rxclassDrugInfoList": { "rxclassDrugInfo": [ { "rxclassMinConceptItem": { "classId": "...", "className": "...", "classType": "ATC" } } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **RXCUI required**: You must supply a valid RXCUI. If you only have a drug name or NDC, resolve it to an RXCUI first using an appropriate node.
- **Classification system options**: The selectable values are fetched from RxClass sources. Ensure you pick a valid option for consistent results.
- **Default selection caveat**: If the default value "ALL" appears as an option, it may not be a valid filter value for the service and could return no results. Prefer selecting a specific source (e.g., ATC) or leaving filtering to other nodes where supported.
- **Output format**: The primary output is a JSON string; downstream nodes expecting structured data should parse it.
- **Error handling**: On request errors, the node returns an error message in the status output and may return a JSON string containing an "error" field.

## Troubleshooting
- **Empty or invalid RXCUI**: If status indicates the RXCUI is empty or invalid, verify the input and consider resolving the RXCUI from a drug name/ndc first.
- **No classes returned**: Switch to a different classification system (e.g., ATC, MeSH, VA) and retry. Some drugs may not be mapped in all systems.
- **API/network errors**: If status reports an API error, retry later or check network connectivity. The "drug_classes" JSON may include an "error" field with details.
- **Unexpected default option**: If using a default like "ALL" yields empty results, explicitly choose a known source such as "ATC".
