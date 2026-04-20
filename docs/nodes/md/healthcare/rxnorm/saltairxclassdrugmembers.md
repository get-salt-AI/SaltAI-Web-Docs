# RxNorm Drugs by Class

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the RxNorm/RxClass service via the RxNormAPI to fetch drugs that are members of a specified drug class. You provide a class identifier and a relationship source (such as ATC or SNOMEDCT), and the node returns a JSON string containing the drug membership payload plus a human-readable status. It is intended for population-style analyses where you need the full set of drugs linked to a given class concept in a particular vocabulary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclassdrugmembers.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a complete list of RxNorm drug concepts associated with a specific drug class for analytics, cohort design, or value set creation. A typical workflow is: (1) identify a class with SaltAIRxClassSearch (search by name) or SaltAIRxClassInfo (look up by ID), (2) pass the confirmed class_id into SaltAIRxClassDrugMembers along with a relationship_source such as ATC, and (3) parse the returned drug_members JSON to extract RxCUIs or names for downstream processing. It fits naturally in SALT/RxNorm/Population Analysis workflows and pairs well upstream with SaltAIRxClassSearch and SaltAIRxClassMembers, and downstream with custom nodes or logic that apply the list of RxCUIs to patient data, claims, or medication inventories. Start with ATC as the relationship_source for standard therapeutic groupings, and always inspect the JSON output in a test run to understand where the member entries (e.g., drugMemberGroup.drugMember.minConcept) are located for your parsing logic.

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
<tr><td style="word-wrap: break-word;">class_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxClass identifier for the drug class whose members you want to retrieve. This is a class ID recognized by the RxClass API (for example, a class concept representing a group of medications). The value must be a non-empty string; if it is empty or only whitespace, the node returns an empty JSON object and an error status without calling the external API.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">relationship_source</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: ATC, ATCPROD, CDC, DAILYMED, FDASPL, FMTSME, MEDRT, RXNORM, SNOMEDCT, VA)</td><td style="word-wrap: break-word;">Source system that defines how the drug class is related to individual drugs. This controls which vocabulary or data source is used for the class-to-drug mapping. For example, ATC uses the Anatomical Therapeutic Chemical classification, SNOMEDCT uses SNOMED CT, RXNORM uses native RxNorm relationships, and VA uses Veterans Affairs drug classes. Must be one of the listed enum values.</td><td style="word-wrap: break-word;">ATC</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_members</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string with the formatted results of the query. The top level includes class_id, relationship_source, and drug_members. The drug_members field is the raw output from the RxClass API, which often contains a drugMemberGroup with a drugMember array; each entry typically has a minConcept object containing fields such as rxcui and name. Downstream components should parse this JSON to extract the list of RxCUIs, drug names, or other attributes needed for analysis or reporting.</td><td style="word-wrap: break-word;">{ "class_id": "D007398", "relationship_source": "ATC", "drug_members": { "drugMemberGroup": { "drugMember": [ { "minConcept": { "rxcui": "83367", "name": "lisinopril 10 MG Oral Tablet", "tty": "SCD" } }, { "minConcept": { "rxcui": "617314", "name": "enalapril 5 MG Oral Tablet", "tty": "SCD" } } ] } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the outcome of the operation. On success, indicates that drug members were successfully retrieved and echoes the class_id and relationship_source used. On failure, contains an error description, such as a message about an empty class ID or an API-level error.</td><td style="word-wrap: break-word;">Successfully retrieved drug members for class ID D007398 with relationship source ATC</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Broad or high-level classes can return a large number of member drugs, which increases payload size and may slow down execution and downstream JSON parsing.
- **Limitations**: If the class_id is invalid, not supported by the chosen relationship_source, or unmapped in RxClass, the response may be empty or contain an error object rather than a populated drug list.
- **Behavior**: When class_id is empty or whitespace-only, the node returns "{}" as drug_members and a status message indicating that the Class ID cannot be empty, without making any external API call.
- **Behavior**: API connectivity or parameter issues are reported through an error field inside the drug_members JSON and a status string beginning with "API Error:", so downstream logic should check both outputs for success or failure.

## Troubleshooting
- **Empty or nearly empty drug_members output with success status**: Inspect the JSON to confirm whether drugMemberGroup or drugMember arrays are empty; if so, verify that the class_id is correct and that the selected relationship_source (for example, ATC) actually defines members for that class.
- **Status is 'Error: Class ID cannot be empty'**: The provided class_id was blank or consisted only of whitespace. Ensure an upstream node or user input supplies a valid RxClass identifier.
- **Status begins with 'API Error:' and drug_members contains an error field**: The RxNorm/RxClass service returned an error (for example, due to invalid parameters, service downtime, or rate limits). Check the detailed error message in the JSON, validate class_id and relationship_source, and retry or implement backoff if rate limiting is suspected.
- **Downstream parsing fails due to unexpected structure**: Run a test execution and inspect the raw drug_members JSON to confirm the actual nesting (for example, drugMemberGroup -> drugMember -> minConcept) and adjust your parsing logic to match the structure returned for your specific query.
