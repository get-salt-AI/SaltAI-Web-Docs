# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a complete workflow to classify a drug and optionally retrieve mechanism-of-action information. It normalizes a provided drug name or NDC to an RxCUI, fetches drug class memberships from selected classification systems, and returns formatted JSON for both classifications and mechanism data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to determine the therapeutic classes for a given drug and optionally include mechanism-of-action details. Typical workflow: provide a drug name (or NDC), choose the classification system scope (e.g., ALL, ATC, VA), and enable mechanism retrieval if desired. The node outputs two JSON strings—one summarizing class memberships, the other for mechanism data—along with a status message.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug to analyze, provided as either a human-readable drug name (e.g., generic or brand) or an NDC code, depending on identifier_type.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specifies how to interpret drug_identifier. Choose 'drug_name' to resolve by name or 'ndc' to resolve by NDC code.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The classification source(s) to consider when fetching classes. Common options include 'ALL' for all systems or a specific source such as 'ATC', 'MEDRT', 'VA', 'RXNORM', etc.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, also returns mechanism-of-action data for the drug's RxCUI when available.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string summarizing the normalized RxCUI, the list of classes the drug belongs to, and a summary (total classes, classification systems encountered, and detected therapeutic categories).</td><td style="word-wrap: break-word;">{"drug_identifier":"atorvastatin","rxcui":"83367","analysis_type":"Drug Class and Mechanism of Action Analysis","classifications":[{"class_id":"D007594","class_name":"HMG CoA Reductase Inhibitors","class_type":"ATC","class_url":"https://rxnav.nlm.nih.gov/REST/rxclass/class/D007594"}],"summary":{"total_classes":1,"classification_systems":["ATC"],"therapeutic_categories":["HMG CoA Reductase Inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing mechanism-of-action information for the drug's RxCUI, when requested and available.</td><td style="word-wrap: break-word;">{"rxcui":"83367","mechanism_analysis":{"mechanismOfActionGroup":{"moa":[{"source":"MEDRT","name":"HMG-CoA Reductase Inhibitor"}]}},"note":"Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable message indicating success or describing any error encountered during the analysis.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input normalization**: The node first resolves the drug to an RxCUI; if resolution fails, outputs will be empty JSON objects with a failure status.
- **Classification scope**: Selecting 'ALL' returns classes across multiple systems; choosing a specific system (e.g., ATC, VA) filters results accordingly.
- **Mechanism availability**: Mechanism-of-action data may be limited depending on source coverage and may not always be returned for every RxCUI.
- **Output format**: Both 'drug_classifications' and 'mechanism_data' are JSON strings intended for downstream parsing or display.
- **Network dependency**: Requires network access to external drug information services; transient API issues may affect results.

## Troubleshooting
- **No RxCUI found for the input**: Verify spelling for 'drug_name' or ensure the 'ndc' is valid and properly formatted. Try an alternative identifier type if available.
- **Empty classifications**: Switch 'classification_system' to 'ALL' to broaden results, or confirm the drug is expected to have class memberships in the chosen system.
- **Mechanism data missing**: Set 'include_mechanism' to true and retry. If still empty, the mechanism may not be available for this RxCUI in the underlying sources.
- **API error messages**: The 'status' field will include details. Retry later or confirm that upstream services are reachable.
- **Unexpected output format**: Ensure downstream nodes parse the output as JSON strings, not as objects.
