# RxNorm Drugs by Class

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves RxNorm drug members for a given drug class identifier from the RxClass system. You specify a class_id and a relationship source (e.g., ATC), and the node returns a formatted JSON string of drug members alongside a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassdrugmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier and want to list all RxNorm drugs associated with that class, filtered by a specific relationship source. It fits into workflows where you begin with a drug class (e.g., ATC code) and need to enumerate the RxNorm drug concepts that belong to it for downstream analysis or display.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier for the drug class you want to query (e.g., an ATC code). Must be a non-empty string.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">relationship_source</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">The source relationship that defines how drugs are linked to the class. Choose one of: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A prettified JSON string containing the requested class_id, selected relationship_source, and the returned drug member data.</td><td style="word-wrap: break-word;">{   "class_id": "D007398",   "relationship_source": "ATC",   "drug_members": { "drugMemberGroup": { "drugMember": [ { "minConcept": { "rxcui": "12345", "name": "ExampleDrug" } } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved drug members for class ID D007398 with relationship source ATC</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Required inputs**: Both class_id and relationship_source are required; an empty class_id will return an error status.
- **Relationship source options**: Only the following values are accepted: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA.
- **Output format**: The drug_members output is a JSON string; parse it if you need to use specific fields programmatically.
- **Upstream data dependencies**: Results depend on the availability and mapping of the selected relationship source in RxClass/RxNorm.
- **Large result sets**: Some classes may return many members; be prepared to handle large JSON payloads.
- **Error propagation**: If the underlying service returns an error, the node will surface it in the JSON and status.

## Troubleshooting
- **Empty class_id**: If status reports 'Error: Class ID cannot be empty', provide a non-empty class_id and re-run.
- **Invalid relationship_source**: If the UI or run fails due to an invalid value, select one of the supported sources (ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA).
- **API Error in output**: If the drug_members JSON contains an "error" field or status begins with 'API Error', verify the class_id is valid for the chosen relationship_source and try again later.
- **No results returned**: If the data section is empty, the class may have no mapped drug members for the chosen relationship_source. Try another source or confirm the class_id.
- **Network/latency issues**: Timeouts or slow responses can cause failures; retry, or run during off-peak hours.
