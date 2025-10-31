# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the drug members for a given RxClass identifier within a specified classification system. Returns the full API payload as JSON plus pre-extracted arrays of member RxCUIs and names for easy downstream use.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass ID and need the list of drugs that belong to that class (e.g., all drugs under an ATC class). Typically, you select the classification system (such as ATC) that the class ID belongs to, run the node to fetch members, and then pass the extracted RxCUIs or names into further analysis or filtering steps.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The RxClass identifier to query. This should be a valid class ID corresponding to the chosen classification system.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">The classification system/source to use when looking up members. Must be one of the supported RxNorm drug class source relations. Choose the system that your class_id belongs to.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the full response for the class members, including the original class_id and data returned by the service.</td><td style="word-wrap: break-word;">{"class_id": "100", "data": {"drugMemberGroup": {"drugMember": [...]}}}</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array of RxCUIs extracted from the class member list.</td><td style="word-wrap: break-word;">["83367", "617314", "617318"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded array of drug names extracted from the class member list.</td><td style="word-wrap: break-word;">["Lisinopril", "Enalapril", "Captopril"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or describing any error encountered.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID 100</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input matching**: Ensure the class_id actually belongs to the selected class_system; mismatches can yield empty results or errors.
- **Output formats**: The members_rxcui and members_name outputs are JSON-encoded arrays (strings). Parse them if you need native array types.
- **Empty handling**: If no members are found or inputs are invalid, the node returns "[]" for the arrays and a descriptive status.
- **System options**: The class_system dropdown is populated from the platform's supported RxNorm drug class source relations; ATC is the default.
- **Data volume**: Some classes may have many members, leading to large JSON payloads in class_members.

## Troubleshooting
- **No results or empty arrays**: Verify the class_id is correct for the chosen class_system and that the class actually has members.
- **API Error in status**: The status will include the error returned by the upstream service. Retry later or adjust inputs.
- **Invalid class_id**: An empty or malformed class_id will produce an error status and empty outputs. Provide a non-empty, valid identifier.
- **Unexpected output format**: Remember that members_rxcui and members_name are JSON strings; parse them before list operations.
