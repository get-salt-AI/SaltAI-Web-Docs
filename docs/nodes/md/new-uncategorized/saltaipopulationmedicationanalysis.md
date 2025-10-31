# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a population-level medication analysis for a given drug class identifier. It fetches class information, retrieves member drugs (RxCUIs), optionally gathers detailed RxNorm and RxTerms data per drug, and compiles a summary JSON describing the class and analyzed drugs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to analyze all medications belonging to a specific drug class (e.g., MeSH/ATC/VA class). Typical workflow: provide a valid class identifier, choose the class system, set whether to include detailed per-drug info and RxTerms, and set a cap on the number of drugs to analyze. The node returns two JSON strings: a population-level analysis summary and per-drug details, along with a status message.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug class identifier used by RxClass (e.g., MeSH descriptor ID, ATC code, or VA class ID).</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Classification system for the provided identifier.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxNorm name and concept properties for each RxCUI.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, fetches RxTerms information for each RxCUI.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drugs (RxCUIs) to analyze from the class members. Range: 1–200.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with population-level analysis summary, including class info, counts, and high-level metadata.</td><td style="word-wrap: break-word;">{"analysis_type":"Population-Level Medication Analysis","class_identifier":"D007398","class_type":"MeSH","population_summary":{"total_drugs":42,"class_name":"...","class_id":"...","analysis_timestamp":"2025-01-01T12:00:00"},"drug_list":["12345","67890"],"drug_details_summary":{"with_rxnorm_details":40,"with_rxterms":39,"errors":2},"analysis_ready":true}</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with per-drug details keyed by RxCUI, including RxNorm name, concept properties, and RxTerms when requested.</td><td style="word-wrap: break-word;">{"total_drugs":42,"summary":{"with_rxnorm_details":40,"with_rxterms":39,"errors":2},"drugs":{"12345":{"rxcui":"12345","rxnorm_name":{"name":"..."},"properties":{"...":"..."},"rxterms":{"...":"..."}},"67890":{"rxcui":"67890","error":"..."}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the outcome of the analysis or an error message if something failed.</td><td style="word-wrap: break-word;">Successfully analyzed 42 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node relies on external RxNorm/RxClass and RxTerms APIs; network issues or API downtime will affect results.
- class_identifier must be valid for the chosen class_type (e.g., MeSH D-code, ATC code, or VA class).
- Outputs are JSON strings; parse them before downstream structured processing.
- max_drugs caps the number of RxCUIs analyzed to manage runtime and API usage (1–200).
- When include_drug_details or include_rxterms is false, corresponding sections in drug_details will be minimal or absent.
- If the class has no drug members or RxCUIs cannot be extracted, the node returns empty JSON strings and a status explaining the issue.

## Troubleshooting
- No RxCUIs found: Verify class_identifier and class_type are correct and correspond to a drug class with members.
- Empty or {} outputs: Check the status output for error details; often caused by invalid identifiers or API errors.
- Rate limiting or timeouts: Reduce max_drugs, or try again later. The node throttles requests slightly, but external limits may still apply.
- Incomplete per-drug details: Set include_drug_details and/or include_rxterms to true. If already true, the external API may have missing data for some RxCUIs.
- Unexpected class metadata: Ensure the identifier truly maps to the intended drug class; class_info is taken directly from the RxClass response.
