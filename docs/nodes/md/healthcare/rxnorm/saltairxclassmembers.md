# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the drug members of a specified RxClass from RxNorm services. Returns the full API payload as JSON plus two convenience lists containing the member RxCUIs and names. Handles empty inputs and API errors and reports a status message for downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier and want to enumerate the drugs that belong to that class. Typical workflow: search or look up a class (e.g., via RxClass Search/Info), pass its class_id and the desired classification system (e.g., ATC) here, then feed the returned member RxCUIs or names into further analysis or filtering nodes.

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
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The classification source/relationship system to query. Must be one of the supported RxClass source relations (e.g., ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA).</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the full response including the class_id and the data payload returned by RxNorm for the class members.</td><td style="word-wrap: break-word;">{"class_id":"100","data":{"drugMemberGroup":{"drugMember":[...]}}}</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array of RxCUIs extracted from the member list for quick downstream use.</td><td style="word-wrap: break-word;">["198211","617314","617318"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array of member drug names corresponding to the RxCUIs.</td><td style="word-wrap: break-word;">["lisinopril","enalapril","ramipril"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or the error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: If class_id is empty or whitespace, the node returns empty payloads and an error status.
- **Classification system**: class_system must be a supported RxClass relationship source (e.g., ATC, MEDRT, RXNORM). Using an unsupported value may return an API error.
- **Output formats**: All outputs are strings. class_members is a JSON string of an object; members_rxcui and members_name are JSON-encoded arrays as strings.
- **Partial or empty results**: If a class has no drug members, members_rxcui and members_name will be empty arrays (as JSON strings).
- **Error propagation**: On API error, class_members contains the error payload as JSON, and status includes an 'API Error:' message.

## Troubleshooting
- **Empty outputs with error status**: Ensure class_id is provided and not blank.
- **API Error in status**: Verify class_system is one of the supported sources (e.g., ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA) and that the class_id exists for that system.
- **members_rxcui or members_name are empty**: The specified class may legitimately have no members for the chosen system. Try a different class_system or confirm the class_id.
- **Downstream parsing issues**: Remember members_rxcui and members_name are JSON strings; parse them as arrays before further processing if your workflow expects list types.
