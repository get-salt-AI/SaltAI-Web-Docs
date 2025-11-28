# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a full drug classification workflow: normalizes a drug identifier to an RxCUI, retrieves drug class memberships across selected classification systems, and optionally adds mechanism-of-action context. Returns JSON-formatted classification results, mechanism data (if requested), and a human-readable status.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug name or NDC and need to understand its therapeutic classes and optionally the mechanism of action. Typical workflow: provide a drug identifier, choose the identifier type (drug name or NDC), select a classification system scope (e.g., ATC or ALL), and decide whether to include mechanism data. The outputs can feed downstream analytics, UI display, or further filtering.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug to analyze, specified by a common name (e.g., atorvastatin) or an NDC code.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select whether the input is a drug_name or an ndc.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which classification source(s) to return. Supports multiple RxClass sources; use ALL to include all supported systems.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, include mechanism-of-action data in the output.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string summarizing the RxCUI, classification entries, and a brief summary (e.g., total classes, systems encountered).</td><td style="word-wrap: break-word;">{ "drug_identifier": "atorvastatin", "rxcui": "83367", "classifications": [ { "class_id": "C123", "class_name": "HMG-CoA Reductase Inhibitors", "class_type": "ATC" } ], "summary": { "total_classes": 1, "classification_systems": ["ATC"], "therapeutic_categories": ["HMG-CoA Reductase Inhibitors"] } }</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with mechanism-of-action data for the RxCUI (when requested), including notes on data coverage.</td><td style="word-wrap: break-word;">{ "rxcui": "83367", "mechanism_analysis": { }, "note": "Mechanism of action data may require integration with additional databases like DrugBank or MeSH" }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or a failure reason.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Identifier type**: Set identifier_type to either "drug_name" or "ndc" to match how you provide drug_identifier.
- **Classification scope**: classification_system supports multiple sources (e.g., ATC, MEDRT, RXNORM, VA, SNOMEDCT, etc.). Use "ALL" to include all supported systems.
- **Mechanism data coverage**: Mechanism-of-action information may be limited and is noted as potentially requiring external sources; the returned section can be empty or minimal.
- **Normalization required**: If the drug cannot be normalized to an RxCUI, outputs will be empty JSON strings and the status will describe the failure.
- **Output format**: All outputs are strings containing JSON; parse them downstream if structured access is required.

## Troubleshooting
- **Empty or invalid identifier**: If status reports failure to get RxCUI, verify drug_identifier spelling for drug_name or provide a valid NDC format (e.g., "00000-0000-00").
- **No classes returned**: Some drugs or identifiers may not map to classes in selected systems. Try setting classification_system to "ALL".
- **Mechanism data is empty**: Not all drugs have mechanism data from the underlying source. This is expected; consider alternate databases if needed.
- **API/Network errors**: If status mentions an API error, retry later or validate network connectivity and service availability.
- **Case sensitivity**: If filtering by a specific system yields no results unexpectedly, reselect the system from the provided choices to avoid case or value mismatches.
