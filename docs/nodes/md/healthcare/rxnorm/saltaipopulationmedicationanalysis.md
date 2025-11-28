# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a population-level medication analysis for a specified drug class. It fetches the class info, collects member drugs (RxCUIs), and optionally enriches each drug with RxNorm properties and RxTerms. Returns a compiled population summary plus per-drug details as JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to analyze all medications within a drug class (e.g., MeSH/ATC/VA) at a population level. Typical workflow: provide the class identifier, choose the classification system, set whether to include per-drug details and RxTerms, optionally cap the number of drugs, then connect the outputs to downstream analytics, visualization, or reporting nodes.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The identifier of the target drug class to analyze (e.g., a MeSH/ATC/VA class ID).</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Classification system for the provided class identifier.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxNorm concept properties and names for each drug.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxTerms information for each drug.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drugs (RxCUIs) to analyze from the class members.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string summarizing the analysis: class metadata, total drugs analyzed, summary stats, and a list of RxCUIs.</td><td style="word-wrap: break-word;">{"analysis_type":"Population-Level Medication Analysis","class_identifier":"D007398","population_summary":{"total_drugs":42}}</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of per-drug details keyed by RxCUI, including RxNorm properties and optional RxTerms, plus a summary section.</td><td style="word-wrap: break-word;">{"total_drugs":42,"summary":{"with_rxnorm_details":40,"with_rxterms":39,"errors":0},"drugs":{"161":{"rxcui":"161","rxnorm_name":{}}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the result of the analysis.</td><td style="word-wrap: break-word;">Successfully analyzed 42 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node depends on external RxClass/RxNorm services via the underlying API; availability and response quality affect results.
- max_drugs limits how many RxCUIs are processed to manage performance and API load.
- When include_drug_details or include_rxterms are enabled, the node performs additional API calls per drug and may take longer.
- If the class identifier has no member drugs, outputs will be empty JSON objects and the status will report no RxCUIs found.
- Returned JSON strings are formatted and intended to be consumed by downstream nodes or saved for analysis.

## Troubleshooting
- Error: Failed to get class information — Verify the class_identifier and class_type are valid and that the external service is reachable.
- Error: Failed to get drug members — The class may not have member drugs or the service may be unavailable; try again or use a different class.
- Status indicates no RxCUIs found — The provided class may be empty or unsupported; confirm the identifier and classification system.
- Drug details summary shows errors > 0 — Some per-drug enrichment calls failed; consider reducing max_drugs or disabling include_rxterms/include_drug_details to isolate issues.
- Slow performance — Lower max_drugs or disable optional details to reduce API calls, and retry later if rate-limited.
