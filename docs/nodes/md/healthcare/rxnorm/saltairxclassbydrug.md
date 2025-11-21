# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug class information for a given RxNorm concept (RXCUI). It queries the RxClass service and returns the classes the drug belongs to, optionally filtered by a specific classification system. Results are returned as a formatted JSON string alongside a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RXCUI for a drug and need to know its classifications (e.g., ATC, MED-RT, SNOMED CT). Commonly used after a step that normalizes a drug name or NDC to an RXCUI. Set the classification system to ALL to get a broad view across sources, or choose a specific system to focus the results.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier for the drug to classify. Must be a non-empty string of digits.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Which classification system to use for results. Use ALL to query across available systems, or select a specific source such as ATC, MEDRT, SNOMEDCT, VA, etc.</td><td style="word-wrap: break-word;">ALL</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string containing the RXCUI, the classification system used, and the classes returned by the service.</td><td style="word-wrap: break-word;">{ "rxcui": "83367", "classification_system": "ALL", "classes": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status indicating success or describing the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validity**: rxcui must be non-empty; empty inputs return "{}" with an error status.
- **Classification scope**: Set classification_system to ALL to include all supported systems; choosing a specific system limits results.
- **Output format**: drug_classes is a JSON string; downstream nodes may need to parse it to access fields.
- **Error propagation**: If the upstream API returns an error, the node returns that error payload as JSON in drug_classes and a status starting with "API Error:".
- **System options**: Valid classification systems are constrained to the supported list (e.g., ALL, ATC, MEDRT, SNOMEDCT, VA, etc.). Using unsupported values will result in an API error.

## Troubleshooting
- **Empty output ("{}") and error status**: Ensure rxcui is provided and not blank.
- **API Error in status**: Verify the classification_system is one of the supported values (e.g., ALL, ATC, MEDRT, SNOMEDCT, VA). Try switching to ALL to test connectivity.
- **Unexpected structure under classes**: The external service can change or vary fields; inspect the JSON to confirm the paths you parse.
- **No classes returned for a valid RXCUI**: The drug may not be mapped in the chosen system; try ALL or a different system.
- **Intermittent failures**: May indicate remote service issues or rate limiting; retry after a short delay.
