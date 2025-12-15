# Drug Classes

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves drug classification data for a given RxNorm Concept Unique Identifier (RXCUI). It queries the RxClass service to return the classes a drug belongs to, optionally filtered by a specific classification system.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassbydrug.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RXCUI for a drug and need to identify its class memberships (e.g., ATC class, VA class). Commonly placed after a step that resolves a drug name or NDC to an RXCUI, it returns a JSON string of classes and a status message.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier for the target drug.</td><td style="word-wrap: break-word;">83367</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Classification system filter. Use "ALL" to return all available systems or specify a particular relationship source (e.g., ATC, MEDRT, VA).</td><td style="word-wrap: break-word;">ALL</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classes</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the requested classes for the provided RXCUI, along with the classification system used.</td><td style="word-wrap: break-word;">{   "rxcui": "83367",   "classification_system": "ALL",   "classes": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status of the operation.</td><td style="word-wrap: break-word;">Successfully retrieved drug classes for RXCUI 83367</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The rxcui input must be a non-empty string; otherwise, the node returns an error status and an empty JSON object.
- **Classification filter**: Setting classification_system to "ALL" returns classes across supported systems; specifying a system restricts results to that source.
- **External dependency**: Results depend on availability and responses from the RxClass/RxNorm services; API errors are surfaced in the output JSON and status.
- **Output format**: The primary output is a JSON-formatted STRING. Downstream nodes should parse it if structured access is needed.

## Troubleshooting
- **Empty or missing RXCUI**: Ensure the RXCUI is provided and valid. If unknown, resolve it first using a node that maps drug names or NDCs to RXCUIs.
- **API Error in status**: If status contains "API Error", inspect the drug_classes JSON for an "error" field with details. Retry or adjust inputs.
- **Unexpected empty classes**: If the response has no classes, try "ALL" for classification_system or verify the RXCUI corresponds to a clinical drug concept recognized by RxClass.
- **Parsing issues downstream**: The drug_classes output is a STRING containing JSON. Ensure your downstream step parses the string before accessing fields.
