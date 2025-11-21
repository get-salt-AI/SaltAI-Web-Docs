# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a population-level medication analysis for a specified drug class. It fetches class info, enumerates member drugs (RxCUIs), and optionally gathers detailed RxNorm concept properties and RxTerms for each drug. Outputs two JSON strings: a high-level population analysis summary and per-drug details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need an overview of all medications within a given drug class (e.g., MeSH, ATC, VA) and optional enrichment with RxNorm concept data and RxTerms. Typical workflow: supply a class identifier, choose whether to include detailed per-drug info, set a cap on the number of drugs to process, then pass the resulting JSON to downstream analytics, visualization, or reporting nodes.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier of the target drug class. This is passed to the RxClass lookups to discover member drugs (RxCUIs).</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Classification system of the identifier. Allowed values: MeSH, ATC, VA. Used for labeling/context in the output.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxNorm concept information per drug (e.g., name and concept properties).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxTerms data per drug.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of RxCUIs to include in the analysis to limit processing and API calls.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the compiled population-level summary, including class info, summary counts, timestamp, and the list of RxCUIs analyzed.</td><td style="word-wrap: break-word;">{"analysis_type":"Population-Level Medication Analysis","class_identifier":"D007398","class_type":"MeSH","population_summary":{"total_drugs":25,"class_name":"...","class_id":"...","analysis_timestamp":"2025-11-21T12:34:56"},"drug_list":["123","456"],"drug_details_summary":{"with_rxnorm_details":20,"with_rxterms":22,"errors":0},"analysis_ready":true}</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string keyed by RxCUI with per-drug details (RxNorm name, properties, RxTerms) and a summary section.</td><td style="word-wrap: break-word;">{"total_drugs":25,"summary":{"with_rxnorm_details":20,"with_rxterms":22,"errors":0},"drugs":{"123":{"rxcui":"123","rxnorm_name":{"...": "..."},"properties":{"...": "..."},"rxterms":{"...": "..."}}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message indicating success or a specific error.</td><td style="word-wrap: break-word;">Successfully analyzed 25 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Network/API dependency**: This node relies on RxClass and RxNorm APIs via an internal RxNormAPI client. Connectivity or API issues will affect results.
- **Input constraints**: max_drugs limits how many RxCUIs are processed; larger values increase runtime and API usage.
- **Optional enrichment**: include_drug_details and include_rxterms control additional API calls per drug; enabling both provides richer data but can be slower.
- **Error handling**: On failures, outputs may return "{}" for population_analysis or drug_details and a status message describing the error.
- **Class type usage**: class_type is used for context/labeling in outputs; the class_identifier is what drives member lookup.

## Troubleshooting
- **Empty results or '{}' outputs**: Verify the class_identifier is valid for the chosen classification system. Try a known ID and reduce max_drugs.
- **API errors in status**: Indicates upstream API or network issues. Retry later, reduce request volume, or disable optional enrichments to lessen load.
- **Slow performance**: Lower max_drugs or disable include_drug_details/include_rxterms. There is a small delay between per-drug calls to avoid overwhelming services.
- **Unexpected class labeling**: Ensure class_type matches the origin of the class_identifier for accurate context in the output.
- **Downstream parsing errors**: Remember outputs are JSON strings. Ensure your next step parses the string into structured data before use.
