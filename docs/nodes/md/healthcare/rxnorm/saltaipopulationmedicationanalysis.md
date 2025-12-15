# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a population-level medication analysis for a given drug class identifier. It fetches the class information, enumerates member drugs (RxCUIs), optionally enriches each drug with RxNorm properties and RxTerms, and compiles a summarized population dataset. Results are returned as JSON strings for downstream analysis and reporting.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to analyze medications across a drug class (e.g., MeSH/ATC/VA class) at a population level. Provide the class identifier and choose whether to include detailed RxNorm properties and/or RxTerms for each drug. Typical workflow: (1) Identify class of interest, (2) Run this node to gather RxCUIs and optional details, (3) Feed the resulting JSON to analytics, visualization, or reporting nodes.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The target drug class identifier used to query RxClass (e.g., a MeSH/ATC/VA class ID).</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">["MeSH", "ATC", "VA"]</td><td style="word-wrap: break-word;">The classification system of the provided class identifier. Used for metadata in the output summary.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, retrieves RxNorm details for each drug (e.g., name and concept properties).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, retrieves RxTerms data for each RxCUI.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drugs (RxCUIs) to analyze from the class membership results. Range: 1–200.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string summarizing the analysis: class metadata, total drugs considered, list of RxCUIs, a summary of enrichment steps, and analysis metadata.</td><td style="word-wrap: break-word;">{ "analysis_type": "Population-Level Medication Analysis", "class_identifier": "D007398", "class_type": "MeSH", "population_summary": { "total_drugs": 42, "class_name": "...", "class_id": "...", "analysis_timestamp": "2025-01-01T12:34:56" }, "drug_list": ["123", "456"], "drug_details_summary": { "with_rxnorm_details": 40, "with_rxterms": 39, "errors": 1 }, "analysis_ready": true }</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string keyed by RxCUI with per-drug enrichment (RxNorm name, properties, and/or RxTerms if requested), plus a summary section.</td><td style="word-wrap: break-word;">{ "total_drugs": 42, "summary": { "with_rxnorm_details": 40, "with_rxterms": 39, "errors": 1 }, "drugs": { "123": { "rxcui": "123", "rxnorm_name": { ... }, "properties": { ... }, "rxterms": { ... } }, "456": { "rxcui": "456", "error": "..." } } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing success or the error encountered.</td><td style="word-wrap: break-word;">Successfully analyzed 42 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: An empty or invalid class identifier will result in empty JSON outputs and a descriptive error message in status.
- **Class type usage**: The class_type input is recorded in the output summary for context; class lookups are performed using the class_identifier.
- **Result limits**: The node will cap the number of analyzed RxCUIs at max_drugs to control workload and API usage.
- **Data availability**: Not all RxCUIs have complete RxNorm properties or RxTerms; missing data will reduce the enrichment counts and may create per-drug errors.
- **External API behavior**: The node relies on drug class and RxNorm endpoints; intermittent API issues may produce partial results or errors.
- **Performance**: A brief delay is inserted between per-drug requests to reduce the chance of rate limiting.

## Troubleshooting
- **Empty results for class**: If population_analysis and drug_details are empty and status mentions no RxCUIs found, verify the class_identifier is correct for the chosen system (MeSH/ATC/VA) and that the class has RxNorm drug members.
- **API Error in status**: If status starts with 'Failed to get class information' or 'Failed to get drug members', retry later or confirm network/API availability. Consider lowering max_drugs.
- **Per-drug errors in details**: Check the drug_details.summary.errors count and inspect the corresponding entries in drug_details.drugs to see which RxCUIs failed and why.
- **Unexpectedly low enrichment counts**: If with_rxnorm_details or with_rxterms are lower than expected, ensure include_drug_details/include_rxterms are set to true and note that not all RxCUIs have full data.
- **Input out of range**: Ensure max_drugs is within 1–200. Values outside this range may be rejected or reset by the system.
- **Malformed JSON downstream**: Outputs are JSON strings. Ensure downstream nodes parse these strings as JSON before attempting structured access.
