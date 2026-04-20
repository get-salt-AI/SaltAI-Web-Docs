# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node executes a complete population-level medication analysis workflow for a specified drug class identifier. It queries RxClass/RxNorm services to obtain class information, enumerate member drugs (RxCUIs), optionally enrich them with detailed RxNorm concept properties and RxTerms data, and then assembles a summarized, analysis-focused JSON structure. The node outputs both a high-level population analysis object and detailed per-drug information, enabling downstream analytics, reporting, and research use cases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand and analyze all medications belonging to a specific pharmacologic or therapeutic class (e.g., ATC, MeSH, VA classes) in a structured, machine-readable format. Typical workflows include population health analytics, pharmacy benefit design, formulary management, and pharmacoepidemiology research, where you want a complete list of drugs within a class plus consistent metadata for further processing. In a pipeline, upstream components typically decide which class to analyze (for example, a UI widget where a user selects an ATC code, or a rules engine that picks a MeSH class). This node then becomes the main aggregation step: it turns that single identifier into a full set of drugs and metadata. Downstream, you would parse the JSON outputs and feed them into nodes that perform statistics, visualization, or documentation. It works naturally with related Rx nodes such as SaltAIRxNormSearch, SaltAIRxNormConceptInfo, and SaltAIRxNormDrugInteractions. For best results, choose the correct class_type for your identifier, keep max_drugs to what you truly need, and only enable include_drug_details and include_rxterms when downstream consumers require that additional metadata.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier of the drug class to analyze in the chosen classification system. This is passed to the RxClass API to retrieve class metadata and its member drugs. It must be valid for the selected class_type (e.g., a MeSH descriptor ID or tree number, an ATC code, or a VA class ID). If the identifier is unknown or unmapped, the node returns empty JSON objects with an error status.</td><td style="word-wrap: break-word;">C10AA (ATC class for HMG CoA reductase inhibitors) or D007398 (MeSH code for a specific pharmacologic class)</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Classification system that interprets the class_identifier. Must be one of: "MeSH", "ATC", or "VA". This directs the RxClass lookup and affects which classes and members are returned. Use MeSH for literature-oriented pharmacologic classes, ATC for widely used therapeutic drug classes, or VA for Veterans Affairs formulary classifications.</td><td style="word-wrap: break-word;">ATC</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to fetch detailed RxNorm information for each member drug. When true, the node retrieves the RxNorm name and concept properties for each RxCUI, adding richer metadata for analytics and reporting. This introduces additional per-drug API calls and increases runtime.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include RxTerms information for each RxCUI. When enabled, the node queries RxTerms to attach patient-facing and prescribing-related fields where available. This is useful for UI and prescribing workflows but adds another API call per drug and can slow execution for larger classes.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drugs (RxCUIs) from the class to include in the analysis. After retrieving members from RxClass, the node truncates the list to this limit before fetching optional details. This keeps API usage and execution time bounded. Accepted values range from 1 to 200 inclusive.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string representing the compiled population-level dataset for the selected drug class. It includes fields such as analysis_type, class_identifier, class_type, raw class_information from RxClass, a population_summary (total_drugs, class_name, class_id, analysis_timestamp), the drug_list of RxCUIs considered, a drug_details_summary, an analysis_ready flag, and a list of use_cases. Designed to be parsed by downstream analytic, reporting, or visualization components.</td><td style="word-wrap: break-word;">{"analysis_type":"Population-Level Medication Analysis","class_identifier":"C10AA","class_type":"ATC","class_information":{"rxclassMinConceptItem":{"classId":"C10AA","className":"HMG CoA reductase inhibitors"}},"population_summary":{"total_drugs":24,"class_name":"HMG CoA reductase inhibitors","class_id":"C10AA","analysis_timestamp":"2026-04-20T14:23:11.482913"},"drug_list":["12345","23456","34567"],"drug_details_summary":{"with_rxnorm_details":24,"with_rxterms":20,"errors":0},"analysis_ready":true,"use_cases":["Public health research","Pharmacy benefit management","Population health analytics"]}</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with detailed information for each analyzed drug RxCUI. The top level usually includes total_drugs, a summary with counts of successful RxNorm details, RxTerms retrieval, and errors, plus a drugs object keyed by RxCUI. Each entry may include rxcui, rxnorm_name, properties (concept properties), and rxterms fields when those options are enabled. This output is ideal for detailed tables, merging with claims or EHR data, or further enrichment steps.</td><td style="word-wrap: break-word;">{"total_drugs":3,"summary":{"with_rxnorm_details":3,"with_rxterms":2,"errors":0},"drugs":{"12345":{"rxcui":"12345","rxnorm_name":"atorvastatin 10 MG Oral Tablet","properties":{"tty":"SCD","name":"atorvastatin 10 MG Oral Tablet"},"rxterms":{"FULL_NAME":"atorvastatin 10 mg tablet","DISPLAY_NAME":"atorvastatin 10 mg tab"}},"23456":{"rxcui":"23456","rxnorm_name":"simvastatin 20 MG Oral Tablet"}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the outcome of the workflow. On success, it states how many drugs were analyzed and repeats the class identifier and type. On failure, it indicates which high-level step failed (for example, fetching class information or drug members) or reports a general error from the analysis pipeline.</td><td style="word-wrap: break-word;">Successfully analyzed 24 drugs in class C10AA (ATC)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node uses multiple external RxNorm/RxClass/RxTerms API calls, including one or more calls per RxCUI when include_drug_details or include_rxterms is enabled, and adds a short delay between calls to avoid overloading the service. Large classes can therefore have noticeable execution times.
- **Performance**: High max_drugs values combined with both details options enabled significantly increase runtime and API traffic; for interactive applications, consider smaller limits and only enable the detail types that are required.
- **Limitations**: If the class_identifier is invalid for the selected class_type or not mapped to any RxCUIs, the node returns "{}" for both JSON outputs and a status message explaining that class information or drug members could not be retrieved or that no RxCUIs were found.
- **Behavior**: Workflow-level failures (such as failure to get class information or drug members) cause the node to return empty JSON strings and a descriptive error status, while errors for individual drugs are captured inside the drug_details.drugs entries and counted in drug_details.summary.errors.
- **Behavior**: The analysis_ready field in the population_analysis output indicates whether the compiled dataset is intended for direct downstream use, even when some individual drug-level calls encountered errors; downstream consumers should also inspect the error counts in drug_details.summary.

## Troubleshooting
- **Invalid or unmapped class**: If the status contains "Failed to get class information" or "Failed to get drug members", verify that class_identifier exists in the chosen class_type and that the correct classification system (MeSH, ATC, VA) is selected. Validate the identifier using an external terminology browser or RxClass tools.
- **Empty results**: If status reads "No RxCUIs found for class ..." and both JSON outputs are effectively empty, it may mean that the class has no mapped RxNorm members or is not supported by the underlying services. Try a different class_identifier or adjust class_type to match the source of your code.
- **Slow execution or timeouts**: When the node takes too long or downstream systems time out, reduce max_drugs and/or disable include_rxterms and include_drug_details to cut down on per-drug API calls. For large-scale analyses, run the node in batch or offline pipelines rather than interactive sessions.
- **Partial drug-level errors**: If drug_details.summary.errors is greater than zero and some entries in drug_details.drugs contain an error field, inspect those messages to identify specific RxCUIs or transient service issues, then rerun the node with a smaller subset of RXCUIs or after waiting for external services to recover.
