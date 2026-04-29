# Population Medication Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs a population-level medication analysis for a specified drug class identifier (MeSH, ATC, or VA). It fetches class information, enumerates member drugs (RxCUIs), optionally enriches each drug with RxNorm properties and RxTerms, and compiles a comprehensive analysis summary. The node returns a high-level population analysis JSON plus a detailed per-drug JSON suitable for analytics, reporting, or downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaipopulationmedicationanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand all medications within a given drug class at a population level, such as for public health studies, formulary reviews, or class-based utilization analysis. A typical workflow is: (1) Identify the class of interest (for example, a MeSH, ATC, or VA class identifier); (2) Configure this node with class_identifier and class_type, and choose whether to include detailed RxNorm properties and RxTerms; (3) Run the node and route population_analysis to analytics, visualization, or LLM-based insight nodes, and route drug_details to detailed clinical or pharmacy analysis steps. Upstream, the class_identifier may come from your own domain metadata or from other Rx-classification nodes. Downstream, this node pairs well with JSON summarization, reporting, and export nodes. For best results, keep max_drugs within reasonable limits, and only enable include_drug_details and include_rxterms when the extra detail is needed, to control runtime and external API load.

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
<tr><td style="word-wrap: break-word;">class_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxClass class identifier for the drug class to analyze. Must be a valid classId in the RxClass system that corresponds to the selected class_type. If invalid or unknown, class lookup and member retrieval will fail and the node will return empty JSON outputs and an error status.</td><td style="word-wrap: break-word;">D007398</td></tr>
<tr><td style="word-wrap: break-word;">class_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Classification system for class_identifier. Supported options are "MeSH" (e.g., D-codes like D007398), "ATC" (e.g., codes like C10AA), and "VA" (Veterans Affairs drug classes). Ensure this matches the ontology used by the class_identifier so that external RxClass calls behave as expected and downstream consumers interpret the class correctly.</td><td style="word-wrap: break-word;">MeSH</td></tr>
<tr><td style="word-wrap: break-word;">include_drug_details</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether per-drug RxNorm details are fetched. When true, the node retrieves RxNorm name and concept properties for each RxCUI in the population. This adds richer context to drug_details but increases the number of external API calls and total runtime. Set to false if you only need the list of RxCUIs and class-level information.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">include_rxterms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether RxTerms data is fetched for each RxCUI. When true, the node adds RxTerms fields (such as display names and route information) for each drug where available. This further increases external requests; disable when RxNorm core concept data is sufficient.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">max_drugs</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of drug RxCUIs to analyze. Must be between 1 and 200. After the member list is retrieved from the class, the node truncates to this number before performing per-drug lookups. Lower values lead to faster runs and reduce the chance of hitting external service limits, especially for large classes.</td><td style="word-wrap: break-word;">50</td></tr>
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
<tr><td style="word-wrap: break-word;">population_analysis</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with the compiled population-level dataset. It includes analysis_type, class_identifier, class_type, raw class_information from RxClass, a population_summary (total_drugs, class_name, class_id, analysis_timestamp), the member RxCUIs in drug_list, a drug_details_summary (with counts such as with_rxnorm_details, with_rxterms, errors), an analysis_ready flag, and a use_cases list. This is the primary artifact to feed into analytics, dashboards, or LLM-based summarization nodes.</td><td style="word-wrap: break-word;">{   "analysis_type": "Population-Level Medication Analysis",   "class_identifier": "D007398",   "class_type": "MeSH",   "class_information": {     "rxclassMinConceptItem": {       "classId": "D007398",       "className": "Hypoglycemic Agents"     }   },   "population_summary": {     "total_drugs": 42,     "class_name": "Hypoglycemic Agents",     "class_id": "D007398",     "analysis_timestamp": "2026-04-27T14:23:11.512394"   },   "drug_list": ["8610", "8611", "8612"],   "drug_details_summary": {     "with_rxnorm_details": 42,     "with_rxterms": 40,     "errors": 0   },   "analysis_ready": true,   "use_cases": [     "Public health research",     "Pharmacy benefit management",     "Academic studies",     "Drug class analysis",     "Population health analytics"   ] }</td></tr>
<tr><td style="word-wrap: break-word;">drug_details</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with per-drug information keyed by RxCUI. The top level contains total_drugs, a summary object (with_rxnorm_details, with_rxterms, errors), and a drugs map where each key is an RxCUI. Each drug entry can include the rxcui, rxnorm_name (from RxNorm), properties (RxNorm concept properties), rxterms (RxTerms payload), and an error field if lookups for that drug failed. This output is intended for detailed clinical, pharmacy, or modeling workflows that require attribute-level data per medication.</td><td style="word-wrap: break-word;">{   "total_drugs": 3,   "summary": {     "with_rxnorm_details": 3,     "with_rxterms": 2,     "errors": 0   },   "drugs": {     "8610": {       "rxcui": "8610",       "rxnorm_name": {         "name": "metformin 500 MG Oral Tablet"       },       "properties": {         "property": [           {"propName": "TTY", "propValue": "SCD"},           {"propName": "SAB", "propValue": "RXNORM"}         ]       },       "rxterms": {         "rxtermsProperties": {           "displayName": "metformin 500 mg tablet",           "route": "ORAL"         }       }     },     "8611": {       "rxcui": "8611",       "rxnorm_name": {"name": "metformin 850 MG Oral Tablet"},       "properties": {"property": []}     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status string describing the result of the run. On success, it reports the number of drugs analyzed and echoes the class identifier and type. On failure, it contains an error message describing what went wrong, such as failing to retrieve class information, failing to retrieve drug members, or a general workflow exception. Use this to control conditional branching or to surface user-facing feedback.</td><td style="word-wrap: break-word;">Successfully analyzed 42 drugs in class D007398 (MeSH)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node makes multiple external calls to RxNorm, RxClass, and RxTerms—typically at least one per RxCUI when details are enabled. Large classes with high max_drugs and both include_drug_details and include_rxterms set to true can be slow and resource-intensive.
- **Performance**: A small delay is inserted between successive per-drug requests to avoid overwhelming external APIs, which further increases runtime when analyzing many drugs.
- **Limitations**: If the class identifier is invalid or the class has no rxnormDrugList returned from RxClass, the node will return "{}" for both JSON outputs and a status indicating that no RxCUIs were found or that class information retrieval failed.
- **Behavior**: Errors for individual RxCUIs (such as failures to fetch RxNorm details or RxTerms) do not abort the entire workflow. They are recorded per-drug in the drugs map with an error field and counted in drug_details.summary.errors.
- **Behavior**: The max_drugs cap is applied after collecting all RxCUIs; only the first N RxCUIs (in the order provided by the external service) are analyzed, which may introduce ordering bias when dealing with very large classes.

## Troubleshooting
- **Class information error**: If status starts with "Failed to get class information" and both outputs are "{}", verify that class_identifier is a valid classId for the chosen class_type and that the external RxClass service is reachable from your environment.
- **No RxCUIs found**: If status reads "No RxCUIs found for class ..." and outputs are empty, confirm that the class actually has member drugs in RxClass, check for typos in the identifier, and try an alternative class that is known to contain medications.
- **Slow execution or timeouts**: If runs are very slow or time out, reduce max_drugs (for example, from 200 to 25–50) and consider disabling include_rxterms and/or include_drug_details to cut down on external calls.
- **Per-drug detail failures**: If drug_details.summary.errors is greater than 0 or some drug entries only contain an error field, check network connectivity and the availability of RxNorm/RxTerms services, then rerun or narrow the analysis to a smaller subset of drugs.
