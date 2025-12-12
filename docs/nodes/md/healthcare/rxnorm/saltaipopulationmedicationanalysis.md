# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a population-level medication analysis for a specified drug class identifier. It fetches class info and member drugs, extracts RxCUIs, optionally enriches each drug with RxNorm concept details and RxTerms data, then compiles a summary dataset. Returns two JSON strings: a population analysis summary and per-drug details, plus a status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need an overview of medications within a drug class (e.g., MeSH, ATC, VA) and optional enrichment of each drug with RxNorm and RxTerms information. Typical workflow: provide a class identifier, decide whether to include detailed RxNorm and RxTerms data, optionally cap the number of drugs analyzed, and pass the outputs downstream for reporting, dashboards, or further analytics.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier for the target drug class (e.g., a MeSH, ATC, or VA class ID). Used to retrieve class information and its member drugs.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">The classification system for the provided identifier. Used as metadata in the analysis output.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxNorm concept details for each RxCUI (e.g., preferred name and concept properties).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxTerms information for each RxCUI (e.g., full name, strength, route, and dose forms).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drugs (RxCUIs) to analyze. Truncates the list if the class contains more.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string summarizing the analysis, including class info, total drugs analyzed, list of RxCUIs, a timestamp, use cases, and a summary of enrichment coverage.</td><td style="word-wrap: break-word;">{"analysis_type":"Population-Level Medication Analysis","class_identifier":"D007398","class_type":"MeSH","population_summary":{"total_drugs":42,"class_name":"...","class_id":"...","analysis_timestamp":"2025-01-01T12:34:56"},"drug_list":["12345","67890"],"drug_details_summary":{"with_rxnorm_details":40,"with_rxterms":38,"errors":0},"analysis_ready":true}</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of per-drug details keyed by RxCUI, including requested RxNorm and RxTerms information and an overall tally of collected details.</td><td style="word-wrap: break-word;">{"total_drugs":42,"summary":{"with_rxnorm_details":40,"with_rxterms":38,"errors":0},"drugs":{"12345":{"rxcui":"12345","rxnorm_name":"Acetaminophen","properties":{...},"rxterms":{...}},"67890":{"rxcui":"67890","error":"..."}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status indicating success or the reason for failure.</td><td style="word-wrap: break-word;">Successfully analyzed 42 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node depends on external RxNorm/RxClass/RxTerms services; network availability and API responsiveness affect results.
- class_type is used as descriptive metadata in the output; ensure class_identifier matches the intended system (MeSH, ATC, or VA).
- max_drugs limits processing for large classes, which can help avoid rate-limits and reduce runtime.
- If include_drug_details and include_rxterms are both false, only the class summary and RxCUI list are produced; per-drug details will be minimal.
- On errors at any stage, the node returns "{}" for one or both JSON outputs with a descriptive status message.
- A small delay is applied between per-drug requests to be polite to upstream APIs; large analyses may take longer.

## Troubleshooting
- No RxCUIs found: Verify the class_identifier is valid for the chosen class_type and corresponds to a class with members.
- API Error messages in status: Check network connectivity and any upstream API limitations; retry later or reduce max_drugs.
- Empty or minimal drug_details: Ensure include_drug_details and/or include_rxterms are set to true; some RxCUIs may lack certain fields.
- Large classes run slowly: Decrease max_drugs to speed up processing and reduce chances of hitting rate limits.
- Unexpected class metadata: Confirm you selected the correct class_type for the identifier; the node does not automatically map across systems.
