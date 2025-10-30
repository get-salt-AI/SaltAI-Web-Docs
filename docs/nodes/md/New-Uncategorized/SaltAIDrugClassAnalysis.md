# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a complete drug classification workflow: normalizes a drug identifier to an RxCUI, retrieves its drug classes from selected classification systems, and optionally includes mechanism of action data. Returns formatted JSON strings for easy downstream consumption and a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltAIDrugClassAnalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug name or NDC and need to identify its RxNorm classes and, optionally, mechanism of action. Typical workflow: provide a drug identifier, choose identifier type and classification system filter, enable mechanism data if needed, then route the JSON outputs to reporting, filtering, or downstream analytics nodes.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug to analyze, either as a free-text drug name or an NDC code depending on identifier_type.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Specifies how to interpret drug_identifier. Choose 'drug_name' for a name string or 'ndc' for an NDC code.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Filter for classification source. Options come from RxClass source/relationship values (e.g., ALL, ATC, MEDRT, SNOMEDCT, VA, etc.).</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes mechanism of action data associated with the RxCUI, when available.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with the normalized RxCUI, a list of class entries (id, name, type, URL), and a summary (total classes, classification systems, inferred therapeutic categories).</td><td style="word-wrap: break-word;">{"drug_identifier":"atorvastatin","rxcui":"83367","classifications":[{"class_id":"D000123","class_name":"HMG-CoA Reductase Inhibitors","class_type":"ATC","class_url":"https://rxnav.nlm.nih.gov/REST/rxclass/class/D000123"}],"summary":{"total_classes":1,"classification_systems":["ATC"],"therapeutic_categories":["HMG-CoA Reductase Inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string with mechanism of action information related to the RxCUI and a note about data sources.</td><td style="word-wrap: break-word;">{"rxcui":"83367","mechanism_analysis":{"mechanism":"Inhibits HMG-CoA reductase"},"note":"Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing the error encountered.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Identifier handling**: The node first normalizes the input to an RxCUI using either a drug name lookup or NDC-to-RxCUI mapping. If normalization fails, outputs will be empty JSON strings and the status explains the failure.
- **Classification filter**: The classification_system dropdown is populated from RxClass source/relations. Selecting a specific system limits the returned classes; using ALL returns all available systems.
- **Case sensitivity caveat**: The internal filter check compares against "all" (lowercase) while the default option is "ALL". If you see unexpected filtering, try selecting a specific system explicitly.
- **Mechanism data availability**: Mechanism of action content depends on available sources and may be limited. The output includes a note indicating that additional integrations (e.g., DrugBank or MeSH) may be needed for completeness.
- **Output format**: All outputs are JSON strings suitable for downstream parsing. The classifications output also includes a simple summary and inferred therapeutic categories (e.g., entries containing 'inhibitor', 'agonist', 'antagonist').

## Troubleshooting
- **No RxCUI found**: Ensure identifier_type matches your input (use 'ndc' for NDC values and 'drug_name' for names). Try a more specific name or validate the NDC format.
- **Empty classifications**: Switch the classification_system to ALL or a different system. Confirm the RxCUI corresponds to the intended drug/strength/form.
- **Mechanism data is empty**: This may be due to source limitations. Consider enabling additional data sources upstream or using a different RxCUI that better represents the active ingredient.
- **API error in status**: Retry later or check network/API access. If persistent, try a different identifier or classification system.
- **Unexpected filtering behavior**: Because of case sensitivity in the internal check, selecting ALL may behave like a specific filter. Manually choose a target system to verify results.
