# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes a drug identifier (drug name or NDC), resolves it to an RxNorm Concept Unique Identifier (RxCUI), queries associated drug classes, and can also fetch mechanism-of-action information. It wraps several RxNorm operations into a single workflow and returns structured JSON describing classifications and mechanisms alongside a human-readable status. Summary fields highlight total classes, classification systems, and heuristic therapeutic categories extracted from class names.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want a single step that converts a drug name or NDC into standardized classifications and mechanism-of-action data. It fits well in medication analytics pipelines, formulary and pharmacology dashboards, research workflows, and prototype clinical-decision tools that need to understand how a drug is categorized and how it works. Place it after nodes or processes that ingest and clean medication data (e.g., EHR exports, CSV imports) so that drug_identifier is already normalized to either a clean name or NDC. Its outputs can feed into downstream nodes that: filter cohorts by therapeutic class, generate textual summaries or reports, visualize class distributions, or combine multiple drugs’ classes for interaction and indication analysis. Use it alongside "SaltAIRxClassByDrug" when you already have an RxCUI and want more targeted class queries, and with "SaltAIRxClassMembers" or "SaltAIRxClassDrugMembers" when you need to move from classes back to member drugs. Choose this node when you prefer a robust, opinionated workflow (automatic RxCUI selection, formatted results, error handling) instead of assembling multiple low-level RxNorm calls yourself.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug you want to analyze, interpreted according to identifier_type. For "drug_name", supply a clear generic or brand name without dosing text or instructions (avoid strings like "atorvastatin 10 mg nightly"). For "ndc", provide a valid National Drug Code string in a format recognized by RxNorm. If this value is empty, badly formatted, or contains too much extraneous text, the RxCUI lookup will likely fail and the node will return empty JSON objects with an error status.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">How to interpret the drug_identifier. Use "drug_name" when passing a textual drug name; the node will call an RxNorm string search and take the first RxCUI from the results. Use "ndc" when passing an NDC code; the node will attempt to resolve that code directly to an RxCUI. If this does not match the real format of drug_identifier (e.g., choosing "drug_name" for an NDC), RxCUI resolution may fail or return unexpected results.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Determines which drug classification systems are returned from RxNorm's getClassesByRxNormDrugId. Options are drawn from RxNormAPI.drug_class_source_relations(), typically including codes such as "ATC", "MEDRT", "VA", plus an option that represents all systems (for example, "ALL"). When set to the "ALL" option, the node requests classes across all available systems; when set to a specific code, only that system’s classes are included. Supplying a value that RxNorm does not support will cause the class lookup to fail and an error to be reflected in the outputs.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to also retrieve mechanism-of-action information for the resolved RxCUI. If true, the node calls RxNormAPI.get_mechanism_of_action and embeds the raw results under mechanism_analysis in the mechanism_data output. If false, the mechanism_data output still retains the same overall JSON structure but mechanism_analysis will generally be empty. Coverage for mechanisms depends on external vocabularies and may be incomplete for some drugs.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string reporting the normalized drug identifier, RxCUI, individual class entries, and a summary. The top level includes fields such as drug_identifier, rxcui, analysis_type, classifications, and summary. Each item in classifications generally has class_id, class_name, class_type (the classification system), and class_url linking to the RxClass REST API. The summary object aggregates total_classes, a list of distinct classification_systems, and a heuristic therapeutic_categories list populated from class names that contain patterns like "inhibitor", "agonist", or "antagonist".</td><td style="word-wrap: break-word;">{"drug_identifier":"atorvastatin","rxcui":"83367","analysis_type":"Drug Class and Mechanism of Action Analysis","classifications":[{"class_id":"C10AA05","class_name":"HMG CoA reductase inhibitors","class_type":"ATC","class_url":"https://rxnav.nlm.nih.gov/REST/rxclass/class/C10AA05"}],"summary":{"total_classes":1,"classification_systems":["ATC"],"therapeutic_categories":["HMG CoA reductase inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string wrapping mechanism-of-action data for the drug. It includes rxcui, a mechanism_analysis object that reflects the structure returned by RxNormAPI.get_mechanism_of_action, and a note explaining that additional databases may be required for full coverage. When include_mechanism is false, mechanism_analysis will typically be an empty object but the rxcui and note fields remain, allowing downstream components to rely on a stable schema.</td><td style="word-wrap: break-word;">{"rxcui":"83367","mechanism_analysis":{"source":"MEDRT","mechanisms":[{"relationship":"has_mechanism_of_action","target":"Inhibits HMG-CoA reductase"}]},"note":"Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable message describing the overall result of the workflow. On success, it confirms that analysis completed and echoes both the original drug identifier and the resolved RxCUI. On failure, it explains what went wrong (for example, failure to obtain an RxCUI or an error from the class lookup) so you can log it, display it to users, or use it for conditional branching.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node may perform several external calls (for RxCUI lookup, class retrieval, and optional mechanism-of-action retrieval), so latency depends on network conditions and RxNorm service responsiveness. Avoid invoking it in tight loops over very large medication lists without considering batching or caching strategies.
- **Limitations**: If the drug_identifier cannot be resolved to an RxCUI due to typos, non-standard naming, or unsupported NDC formats, both JSON outputs will be empty or minimal (often "{}"), and the status message will indicate that RxCUI retrieval failed.
- **Behavior**: For identifier_type="drug_name", the node always uses the first RxCUI returned by the RxNorm name search. This may correspond to a default or most common formulation rather than a specific strength or route; use NDCs or more controlled lookups if you need exact product mappings.
- **Behavior**: The therapeutic_categories list in the summary is built using simple keyword checks in class_name (for example, detecting "inhibitor", "agonist", or "antagonist"); it is meant as a convenience signal and should not be treated as a definitive clinical classification.
- **Limitations**: Mechanism-of-action data is not guaranteed for every drug. Even when include_mechanism is true, mechanism_analysis may be sparse or empty if the underlying vocabularies do not provide detailed mechanism data for that RxCUI.

## Troubleshooting
- **Failed to get RxCUI**: If status contains text like "Failed to get RxCUI for drug_name: ..." and both outputs are "{}" or otherwise empty, check that drug_identifier is correctly spelled, remove dosage and instruction text, or change identifier_type to match the actual format (for example, switch to "ndc" for an NDC code).
- **Classification API error**: If status reads "Failed to get classifications: ..." and the drug_classifications JSON includes an "error" field, verify that classification_system is one of the supported values exposed by RxNormAPI.drug_class_source_relations() and ensure that the RxNorm service is accessible from your environment.
- **Empty mechanism_analysis despite include_mechanism=true**: When mechanism_data returns an empty or very small mechanism_analysis, this typically indicates limited upstream data rather than a node failure. Confirm that the RxCUI is correct and, if necessary, augment with other pharmacology data sources downstream.
- **Unexpectedly few or missing classes**: If total_classes or classification_systems in the summary appears incomplete, rerun the node with classification_system set to the "ALL" option so that results are not limited to a single system, then inspect the classifications array to understand what was returned.
