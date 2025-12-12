# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the member drugs for a specified RxClass. Returns the raw member data as JSON plus two convenience lists: the RxCUI identifiers and the drug names extracted from the response. Provides a status message indicating success or the nature of any error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier and want to list all drug members belonging to that class (e.g., to drive downstream lookups or filtering). A common workflow is: search or look up a class to obtain its class_id, then pass that class_id and the appropriate class_system into this node to enumerate its members.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier for the drug class whose members you want to retrieve.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">The classification system or relationship source to use for member retrieval. Typical values include ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, and VA.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the full response of class members for the given class_id and class_system.</td><td style="word-wrap: break-word;">{"class_id":"D007398","data":{"drugMemberGroup":{"drugMember":[...]}}}</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array (as a string) of RxCUI identifiers extracted from the member list.</td><td style="word-wrap: break-word;">["83367","617314","617312"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array (as a string) of member drug names extracted from the member list.</td><td style="word-wrap: break-word;">["Lisinopril","Enalapril","Ramipril"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing the result of the operation.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID D007398</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Class ID required**: The class_id input cannot be empty; an empty value will produce an error status with empty results.
- **Match system to class**: Ensure class_system matches the source/relationship of the class referenced by class_id (e.g., ATC classes should use class_system=ATC) to avoid empty or inconsistent results.
- **Output formats**: The members_rxcui and members_name outputs are JSON arrays serialized as strings. Parse them before downstream iteration if needed.
- **Empty member groups**: If no members are found or the response lacks a member list, the node returns valid JSON with empty arrays for the convenience outputs.
- **External service behavior**: Results depend on upstream RxClass/RxNorm services and may be subject to data availability and service rate limits.

## Troubleshooting
- **Empty members_rxcui or members_name**: Verify the class_id is valid and the class_system matches the class's source (e.g., try ATC if the class is an ATC class).
- **Status shows 'API Error: ...'**: The upstream service returned an error. Retry later, confirm inputs, or switch to another classification system if applicable.
- **Status shows 'Error: Class ID cannot be empty'**: Provide a non-empty class_id.
- **Unexpected output structure in class_members**: The underlying data may differ by class system or current API version. Inspect class_members JSON to adapt downstream parsing.
