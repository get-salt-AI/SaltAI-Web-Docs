# RxNorm Drugs by Class

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the set of RxNorm drug members associated with a given drug class identifier, filtered by a specified relationship source. Returns a JSON string containing the class ID, relationship source, and the raw drug members payload from the RxNorm service, along with a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassdrugmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug class identifier and need to enumerate its RxNorm drug members for downstream analysis or filtering. Typical workflows include: searching or selecting a drug class, then using this node to gather member drugs, followed by parsing or joining with other drug metadata.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The identifier of the drug class to query. Must be a valid RxClass/controlled vocabulary class ID recognized by the selected relationship source.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">relationship_source</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The classification or relationship source to use when retrieving class membership. Determines which system's class-to-drug relationships are returned.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the query context and the raw drug members payload. Structure: { "class_id": ..., "relationship_source": ..., "drug_members": <RxNorm response> }.</td><td style="word-wrap: break-word;">{   "class_id": "D007398",   "relationship_source": "ATC",   "drug_members": { /* RxNorm response object */ } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable message indicating success or the nature of any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved drug members for class ID D007398 with relationship source ATC</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: class_id must be non-empty; otherwise the node returns an empty JSON object and an error status.
- **Source selection**: relationship_source must be one of: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA. Results vary by source coverage.
- **Output format**: drug_members is a stringified JSON object. Consumers should parse it before accessing fields.
- **API behavior**: If the underlying service returns an error, the node passes it through within the JSON and sets status to an API error message.
- **Empty results**: For some class/source combinations, the service may return no members; the payload may omit expected arrays. Handle missing fields defensively.
- **Networking and rate limits**: Errors due to connectivity or service limits will result in an error status and an empty JSON string for the primary output.

## Troubleshooting
- **Received '{}' and an error status**: Ensure class_id is provided and not just whitespace.
- **API Error in status**: The relationship_source may be incompatible with the class_id, or the service encountered an issue. Try a different source or verify the class ID.
- **Parsed JSON lacks expected arrays**: Different sources return different structures; inspect the raw drug_members payload and add null checks before iterating.
- **Unexpectedly few or no results**: Switch relationship_source (e.g., from ATC to MEDRT or RXNORM) to compare coverage, or validate the class belongs to the chosen source.
- **Downstream parser fails**: Confirm you are JSON-parsing the drug_members string before accessing nested fields.
