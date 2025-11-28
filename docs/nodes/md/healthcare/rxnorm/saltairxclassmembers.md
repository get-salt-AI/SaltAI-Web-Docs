# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the drug members of a specified RxClass. Given a class ID and a classification system/source, the node calls the RxNorm service to return the full member payload and also extracts parallel lists of member RxCUIs and names.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to enumerate the drugs that belong to a specific RxClass (e.g., all members of an ATC class). It fits into workflows where you first identify or search for a class, then gather its member drugs for downstream analysis, filtering, or display.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier for the class whose members you want to retrieve.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The classification system/source to query for class membership. Select from the provided options (e.g., ATC, MEDRT, RXNORM, SNOMEDCT, VA, etc.).</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the full API response for members of the specified class, wrapped with the provided class_id.</td><td style="word-wrap: break-word;">{ "class_id": "100", "data": { "drugMemberGroup": { "drugMember": [ { "minConcept": { "rxcui": "12345", "name": "ExampleDrug" } } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array (as a string) of member RxCUIs extracted from the response.</td><td style="word-wrap: break-word;">["12345", "67890"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array (as a string) of member drug names extracted from the response.</td><td style="word-wrap: break-word;">["ExampleDrug", "AnotherDrug"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Status message indicating success or describing the encountered error.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: If class_id is empty or only whitespace, the node returns class_members = "{}", members_rxcui = "[]", members_name = "[]", and an error status.
- **API errors**: If the RxNorm service returns an error, class_members contains the error JSON, members_rxcui and members_name are "[]", and status includes the API error message.
- **Output formats**: members_rxcui and members_name are JSON-encoded arrays returned as strings. Parse them to arrays before further processing.
- **Class system options**: The class_system must be selected from the provided list of supported sources (e.g., ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA).
- **Data shape**: Member extraction relies on the presence of data.drugMemberGroup.drugMember[].minConcept.rxcui/name. If this structure is absent, members lists will be empty.

## Troubleshooting
- **Empty members with success status**: The class may have no members in the selected class_system or the structure drugMemberGroup.drugMember is missing. Try a different class_system or verify the class_id.
- **"Error: Class ID cannot be empty"**: Provide a non-empty class_id.
- **API Error in status**: The upstream RxNorm service returned an error. Check the class_id and class_system, wait and retry in case of transient issues, or switch to a different source.
- **Members lists are strings**: If downstream nodes expect arrays, parse members_rxcui and members_name from JSON strings into arrays first.
- **Unexpected data fields**: If the member fields differ (e.g., missing minConcept), confirm the class_system and the available fields for that source; behavior depends on the RxNorm API response.
