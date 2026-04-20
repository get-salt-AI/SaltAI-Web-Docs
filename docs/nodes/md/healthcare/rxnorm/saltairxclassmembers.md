# RxClass Members

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the RxNorm RxClass API to fetch all drug members belonging to a specified drug class. It returns the full members response as a formatted JSON string and also extracts aligned lists of RxNorm concept identifiers (RxCUIs) and corresponding drug names. It is intended for workflows that move from class-level information to detailed per-drug processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an RxClass identifier (class_id) and want to obtain the drugs associated with that class in a specific classification system. A typical workflow is: (1) use `SaltAIRxClassSearch` to discover candidate classes by name, (2) optionally inspect and validate a selected class with `SaltAIRxClassInfo`, and (3) pass the confirmed class_id and appropriate class_system into `RxClass Members` to expand the class into its constituent drugs. The members_rxcui output can then be consumed by analysis nodes such as `SaltAIRxClassByDrug`, `SaltAIDrugClassAnalysis`, or any downstream node that operates per RxCUI (e.g., mechanism-of-action lookup, label summarization, or interaction analysis). Position this node where you need to fan out from a single class into multiple drugs. Always ensure that class_system matches the origin of your class_id (for example, ATC for ATC-based class IDs), and validate that class_id is non-empty, as the node will short-circuit with an error if it is blank.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxClass identifier for the drug class whose members you want to retrieve. This must be a valid class ID in the selected classification system. The node validates that it is not empty or whitespace-only; otherwise, no API call is made and an error status is returned.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_system</td><td>True</td><td style="word-wrap: break-word;">ENUM(drug_class_source_relations)</td><td style="word-wrap: break-word;">Classification system or relationship source under which the class_id is defined. Valid options are provided by RxNormAPI.drug_class_source_relations() and typically include sources such as ATC and other RxClass-supported systems. This must correspond to the system where the class_id exists; mismatches can lead to empty or partial member lists.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">class_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the RxClass members response, wrapped with the class_id. Structure: {"class_id": <class_id>, "data": <rxclass_members_response>}. The data section usually includes a drugMemberGroup.drugMember array with each drug's minimal concept (minConcept) details.</td><td style="word-wrap: break-word;">{"class_id": "D007398", "data": {"drugMemberGroup": {"drugMember": [{"minConcept": {"rxcui": "83367", "name": "atorvastatin 10 MG Oral Tablet", "tty": "SCD"}}, {"minConcept": {"rxcui": "617314", "name": "atorvastatin 20 MG Oral Tablet", "tty": "SCD"}}]}}}</td></tr>
<tr><td style="word-wrap: break-word;">members_rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of RxNorm CUIs (RxCUIs) for each drug member of the class, collected from minConcept.rxcui. This list is useful for downstream nodes that accept RxCUI arrays as input.</td><td style="word-wrap: break-word;">["83367", "617314", "617315"]</td></tr>
<tr><td style="word-wrap: break-word;">members_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of human-readable drug names for each member, collected from minConcept.name. The list is index-aligned with members_rxcui, so members_rxcui[i] corresponds to members_name[i].</td><td style="word-wrap: break-word;">["atorvastatin 10 MG Oral Tablet", "atorvastatin 20 MG Oral Tablet", "atorvastatin 40 MG Oral Tablet"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message summarizing the result of the operation, including success, validation errors, or API errors propagated from the RxNorm service.</td><td style="word-wrap: break-word;">Successfully retrieved RxClass members for class ID D007398</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node makes a live request to the RxNorm RxClass service; classes with many member drugs can result in larger responses and increased execution time.
- **Limitations**: If the RxClass response lacks a drugMemberGroup or drugMember section, the node returns empty members_rxcui and members_name lists even when no explicit API error is present, which usually indicates there are no members for that class/system combination.
- **Behavior**: When class_id is empty or whitespace-only, the node skips the API call and returns "{}" for class_members, "[]" for members_rxcui and members_name, and a status message stating that the Class ID cannot be empty.
- **Behavior**: If the RxNorm API returns an error (via an "error" field), the node serializes that error into class_members, sets members_rxcui and members_name to "[]", and returns a status starting with "API Error:" that includes the underlying message.

## Troubleshooting
- **Empty member lists with no explicit error**: Check the class_members JSON to see whether drugMemberGroup.drugMember exists. If it does not, verify the class_id and class_system pair, and confirm via `SaltAIRxClassInfo` that the class is expected to have drug members.
- **Status shows 'Error: Class ID cannot be empty'**: The class_id field was blank or only whitespace. Provide a valid class_id, typically sourced from `SaltAIRxClassSearch` or `SaltAIRxClassInfo`.
- **Status shows 'API Error: ...'**: The RxNorm API reported an error. Inspect the error details inside class_members, verify network connectivity, and confirm that class_id and class_system are valid for RxNorm.
- **Fewer members than expected**: Different classification systems may define different memberships. Try another class_system from RxNormAPI.drug_class_source_relations() that better matches the origin of your class codes, and cross-check expectations using `SaltAIRxClassInfo`.
