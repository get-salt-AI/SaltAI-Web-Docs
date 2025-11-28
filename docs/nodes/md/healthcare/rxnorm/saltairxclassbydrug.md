# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug classification information for a given RxNorm Concept Unique Identifier (RXCUI). You can optionally filter the results by a specific classification system. Returns the raw classification data as a JSON string along with a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RXCUI for a drug and need to see its associated classes (e.g., ATC classes). Commonly, you first resolve a drug name or NDC to an RXCUI using an RxNorm lookup node, then pass that RXCUI here to fetch its classifications. Set a specific classification system to narrow results or use the default to retrieve all available classes.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxNorm Concept Unique Identifier for the drug whose classes you want to retrieve.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The classification system to filter results by. If set to ALL, returns classes from all supported systems.</td><td style="word-wrap: break-word;">ALL</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the RXCUI, the selected classification system, and the classes returned by the service.</td><td style="word-wrap: break-word;">{ "rxcui": "83367", "classification_system": "ALL", "classes": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The RXCUI must be a non-empty string; an empty value will return an error status.
- **Filtering**: Set classification_system to ALL to return classes across all supported systems, or choose a specific system to narrow results.
- **Output format**: The primary output is a JSON string. Downstream nodes expecting structured data should parse this string.
- **Error handling**: If the underlying service returns an error, the first output will contain a JSON error payload and the status will include an error message.
- **Rate limits and availability**: Results depend on availability and responsiveness of the underlying RxNorm/RxClass service; network errors may occur.

## Troubleshooting
- **Empty or invalid RXCUI**: If status reports 'Error: RXCUI cannot be empty' or results are empty, verify the RXCUI was resolved correctly from a prior step.
- **No classes returned**: Ensure the RXCUI corresponds to a clinical drug concept that is classifiable. Try switching classification_system to ALL.
- **API Error in status**: The status may include 'API Error: ...'. Inspect the first output JSON for details and retry later in case of transient service issues.
- **Unexpected classification_system behavior**: If filtering yields no results, confirm the selected classification system is supported and correctly spelled. Try ALL to validate baseline results.
- **Downstream parsing failures**: If a subsequent node fails, ensure you parse the 'drug_classes' JSON string into structured data before further processing.
