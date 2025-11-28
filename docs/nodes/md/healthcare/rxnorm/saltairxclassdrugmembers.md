# RxNorm Drugs by Class

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the list of RxNorm drug members for a given drug class ID, constrained by a selected relationship source (e.g., ATC, MEDRT). Returns a JSON string with the original API data bundled alongside your inputs and a status message. Useful for enumerating drugs associated with a therapeutic or classification class.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassdrugmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug class identifier and want to obtain all RxNorm drug members linked to that class from a specific source system. Typical workflow: look up or select a class ID, choose the relationship source that defines the class membership (such as ATC), then pass the output JSON to downstream nodes for filtering, reporting, or further analysis.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The identifier of the drug class to query. Must be a valid class ID known to the chosen relationship source.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">relationship_source</td><td>True</td><td style="word-wrap: break-word;">ATC \| ATCPROD \| CDC \| DAILYMED \| FDASPL \| FMTSME \| MEDRT \| RXNORM \| SNOMEDCT \| VA</td><td style="word-wrap: break-word;">Selects the source system that defines the class-to-drug relationships. This determines which set of drug members are returned for the given class ID.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the query context and results: { "class_id": ..., "relationship_source": ..., "drug_members": <raw API response> }.</td><td style="word-wrap: break-word;">{ "class_id": "D007398", "relationship_source": "ATC", "drug_members": { "drugMemberGroup": { "drugMember": [ { "minConcept": { "rxcui": "...", "name": "..." } } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully retrieved drug members for class ID D007398 with relationship source ATC</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: The node returns an error status if class_id is empty.
- **Source sensitivity**: Results depend on the chosen relationship_source; a class_id may be valid in one source and not in another.
- **Output format**: The first output is a JSON string, not a parsed object. Downstream steps should parse it before structured use.
- **API passthrough**: If the underlying service reports an error, it is returned as a JSON string in the first output with an 'API Error' status.
- **Result size**: Some classes can return many members, which may produce large JSON outputs.
- **Defaults**: Default class_id is "D007398" and default relationship_source is "ATC".

## Troubleshooting
- **Empty or invalid class_id**: Ensure class_id is not blank and matches a valid identifier in the selected relationship_source.
- **No results returned**: Try a different relationship_source or verify that the class exists within that source.
- **Unexpected JSON structure**: The raw API response is embedded under 'drug_members'; check for keys like 'drugMemberGroup' and 'drugMember'.
- **Downstream parsing errors**: Confirm that you are parsing the first output as JSON text before extracting fields.
- **API Error status**: Inspect the first output JSON for an 'error' field; correct the inputs or try again later if the service is temporarily unavailable.
