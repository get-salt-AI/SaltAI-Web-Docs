# RxNorm Drugs by Class

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the list of RxNorm drug members associated with a specified drug class. You provide a class identifier and a relationship source (e.g., ATC, MEDRT), and the node returns a JSON string with the drugs linked to that class and a status message. It validates inputs and surfaces API errors in the output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassdrugmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to enumerate RxNorm drugs belonging to a particular pharmacologic or therapeutic class. Typical workflow: obtain or select a class ID (e.g., from a class search or external catalog), choose the relationship source system (e.g., ATC), then pass both to this node to get drug members as JSON for downstream analysis or visualization.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier of the drug class to query. Accepts IDs from supported classification systems; example default corresponds to a MeSH/ATC-style class code.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">relationship_source</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA)</td><td style="word-wrap: break-word;">Classification system source to use when retrieving members. Determines which relationship set is used to link drugs to the class.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the request context and the retrieved drug members. Structure includes class_id, relationship_source, and the raw API results under drug_members.</td><td style="word-wrap: break-word;">{"class_id":"D007398","relationship_source":"ATC","drug_members":{...}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status of the operation. Contains success confirmation or an error message if validation or API calls fail.</td><td style="word-wrap: break-word;">Successfully retrieved drug members for class ID D007398 with relationship source ATC</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node returns JSON as a STRING; downstream nodes may need to parse it.
- class_id must be non-empty; an empty value returns an error status and an empty JSON object.
- relationship_source must be one of the allowed values: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA.
- Results reflect the selected relationship source and may differ across systems.
- Network/API availability and rate limits can affect responses; API errors are returned in the drug_members JSON and summarized in the status string.

## Troubleshooting
- If status reports 'Error: Class ID cannot be empty', provide a valid class_id string.
- If status contains 'API Error: ...', verify the class_id/source combination is valid and retry after checking network connectivity or API limits.
- If drug_members is an empty list or missing expected members, try a different relationship_source or confirm the class_id corresponds to that system.
- If downstream parsing fails, ensure you JSON-parse the drug_members output before accessing fields.
