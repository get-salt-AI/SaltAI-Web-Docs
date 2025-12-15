# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a full workflow to classify a drug and optionally retrieve its mechanism of action. It normalizes a provided drug name or NDC to an RxCUI, fetches drug classes from RxClass, and formats summary information including classification counts and inferred therapeutic categories.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to determine the therapeutic classes associated with a drug and optionally include mechanism-of-action data. Typical flow: provide a drug name (or NDC), choose the identifier type, optionally limit to a specific classification system, and decide whether to include mechanism data. Connect its outputs to downstream nodes for reporting, filtering, or population analyses.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug identifier to analyze. Accepts a drug name (e.g., generic or brand) or an NDC, depending on identifier_type.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">["drug_name", "ndc"]</td><td style="word-wrap: break-word;">Specifies how to interpret drug_identifier. Choose "drug_name" to resolve by name, or "ndc" to resolve by National Drug Code.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Which classification relationship sources to include when fetching classes. Use "ALL" to include all supported systems, or select a specific source such as ATC, MEDRT, VA, etc.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, retrieves and returns mechanism of action data for the resolved RxCUI.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with the resolved RxCUI, a list of class entries (id, name, type, URL), and a summary with total classes, classification systems, and inferred therapeutic categories.</td><td style="word-wrap: break-word;">{"drug_identifier": "atorvastatin", "rxcui": "83367", "classifications": [{"class_id": "D001241", "class_name": "HMG-CoA Reductase Inhibitors", "class_type": "ATC", "class_url": "https://rxnav.nlm.nih.gov/REST/rxclass/class/D001241"}], "summary": {"total_classes": 1, "classification_systems": ["ATC"], "therapeutic_categories": ["HMG-CoA Reductase Inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing mechanism of action information keyed to the RxCUI, plus a note about possible external data sources.</td><td style="word-wrap: break-word;">{"rxcui": "83367", "mechanism_analysis": {"moa": [{"source": "MEDRT", "name": "HMG-CoA reductase inhibitor"}]}, "note": "Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or an error condition.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Identifier resolution**: The node first resolves the provided drug_identifier to an RxCUI. If resolution fails, both outputs will be empty JSON objects and status will contain an error.
- **Classification scope**: Set classification_system to "ALL" to include all supported sources or select a specific source (e.g., ATC, MEDRT, VA) for narrower results.
- **Mechanism data**: When include_mechanism is true, mechanism_data is fetched and returned as-is with minimal formatting and may rely on external sources; availability can vary by RxCUI.
- **Output format**: All outputs are JSON strings; downstream nodes expecting structured data should parse these strings.
- **Therapeutic categories**: The summary infers therapeutic_categories by scanning class names for terms like "inhibitor", "agonist", or "antagonist"; this is heuristic and not exhaustive.
- **Case sensitivity note**: If selecting all classification systems, prefer "ALL" as exposed by the input selector.

## Troubleshooting
- **Empty outputs with error status**: The RxCUI could not be resolved. Verify the drug name spelling or NDC format and ensure identifier_type matches the input.
- **No classes returned**: The selected classification_system may not have entries for the drug. Try "ALL" or a different source (e.g., ATC, MEDRT).
- **Mechanism data is empty**: Not all drugs have mechanism data available from the backing sources. Set include_mechanism to true, and if still empty, consider augmenting with external datasets.
- **JSON parsing errors downstream**: Both main outputs are JSON strings. Ensure you parse the strings before accessing fields.
- **Unexpected classification systems in summary**: Multiple sources may be returned when using "ALL"; filter results downstream if you need a single system.
