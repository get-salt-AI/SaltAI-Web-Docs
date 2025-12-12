# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs an end-to-end analysis to identify a drug’s RxNorm Concept (RxCUI), retrieve its drug class memberships across supported classification systems, and optionally include mechanism-of-action information. Returns structured JSON summaries with classes, counts, and categorized highlights.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a drug name or NDC and need a consolidated view of its therapeutic classes and mechanism of action. Typical workflow: provide a drug identifier, choose identifier type, optionally filter by a specific classification system (or request all), and include mechanism data for richer clinical context. The outputs can feed downstream clinical NLP, decision support, or reporting nodes.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug identifier to analyze. Accepts a common drug name (e.g., generic or brand) or an NDC, depending on identifier_type.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies how to interpret drug_identifier.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Which classification system(s) to return. Choose a specific source or request all supported relations.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes mechanism-of-action data for the RxCUI (when available).</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string summarizing the analyzed drug, its RxCUI, a list of class entries (class_id, class_name, class_type, class_url), and a summary with total_classes, classification_systems, and therapeutic_categories.</td><td style="word-wrap: break-word;">{"drug_identifier":"atorvastatin","rxcui":"83367","analysis_type":"Drug Class and Mechanism of Action Analysis","classifications":[{"class_id":"D000123","class_name":"HMG-CoA Reductase Inhibitors","class_type":"ATC","class_url":"https://rxnav.nlm.nih.gov/REST/rxclass/class/D000123"}],"summary":{"total_classes":1,"classification_systems":["ATC"],"therapeutic_categories":["HMG-CoA Reductase Inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with mechanism-of-action results tied to the RxCUI. Includes a pass-through of mechanism data and a note about data sources.</td><td style="word-wrap: break-word;">{"rxcui":"83367","mechanism_analysis":{},"note":"Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or error.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Classification system selection may be case-sensitive. To request all available classes, ensure you select the explicit 'all/ALL' option provided in the UI options.
- If the drug cannot be normalized to an RxCUI (e.g., unrecognized name or invalid NDC), the node returns empty JSON for outputs and a failure status.
- Mechanism-of-action data availability varies and may be limited; the node returns an empty mechanism dataset if none is available.
- The classifications output includes a summary that deduplicates classification systems and highlights likely therapeutic categories by simple keyword detection (e.g., inhibitor, agonist, antagonist).
- Inputs must not be empty; whitespace-only inputs will result in error status.

## Troubleshooting
- No RxCUI found for input: Confirm the identifier_type matches the drug_identifier (e.g., set identifier_type to 'ndc' for NDC values) and verify the spelling/format.
- Empty or sparse classifications: Try selecting the 'all' or 'ALL' classification option to broaden coverage, or verify the RxCUI corresponds to the intended ingredient/form.
- Mechanism data missing: This can be normal if the source lacks data for the RxCUI. Set include_mechanism to true and, if needed, integrate with external drug knowledge bases.
- API error in results: Retry later, verify network connectivity, or adjust the classification_system to a supported value from the provided options.
