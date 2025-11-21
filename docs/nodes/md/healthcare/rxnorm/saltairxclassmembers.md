# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the drug members of a specified RxClass and returns both a full JSON payload and convenient lists of member RxCUIs and names. It queries the RxNorm RxClass service for the given class ID and classification system, and formats results for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier and want to enumerate the drugs that belong to that class. It fits into workflows where you first search or look up a class (e.g., via RxClass Search or RxClass Information), then pass the class ID here to obtain the member drugs for analysis, filtering, or display.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier whose drug members you want to retrieve.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The drug class system/source relation to query. Must be a valid value supported by the RxNorm API (e.g., ATC).</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the full response for the class members, including the original class_id and returned data.</td><td style="word-wrap: break-word;">{"class_id":"100","data":{...}}</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array (as a string) of RxCUIs for the member drugs extracted from the response.</td><td style="word-wrap: break-word;">["153839","1234","5678"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array (as a string) of drug names corresponding to the member RxCUIs.</td><td style="word-wrap: break-word;">["lisinopril","enalapril","captopril"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or error.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The class_id must not be empty; otherwise the node returns empty arrays and an error status.
- **Class system values**: class_system must be a valid source relation supported by the RxNorm API. Using an unsupported value may return an API error.
- **Output formats**: members_rxcui and members_name are JSON arrays serialized as strings for compatibility. Parse them back to arrays if you need to iterate.
- **Empty results**: If a class has no members or the response lacks a drugMemberGroup, the members arrays will be empty.
- **Error propagation**: If the underlying API returns an error, the class_members output contains the error JSON and the status starts with "API Error:".

## Troubleshooting
- **Error: Class ID cannot be empty**: Provide a non-empty class_id obtained from a prior class search or info lookup.
- **API Error in status**: Verify the class_id and class_system are correct and supported. Retry if the RxNorm service is temporarily unavailable.
- **Empty members arrays**: The class may legitimately have no members for the selected system. Try a different class_system or confirm the class ID is correct.
- **Unexpected output parsing**: Remember members_rxcui and members_name are JSON strings. Parse them before use in tools expecting arrays.
