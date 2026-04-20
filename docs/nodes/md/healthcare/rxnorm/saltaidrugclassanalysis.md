# Drug Class Analysis

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node normalizes a drug identifier (name or NDC) to an RxNorm RXCUI, then retrieves all associated drug classes and optionally mechanism-of-action information. It consolidates these results into JSON objects that list each class with IDs, names, types, URLs, and a summary of class counts, systems, and inferred therapeutic categories. The node is designed for clinical and pharmacology workflows that require standardized, machine-readable drug classification from minimal user or system input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltaidrugclassanalysis.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to go from a simple drug input (e.g., "atorvastatin" or an NDC code) to a structured understanding of that drug's therapeutic classes and mechanism-of-action content. Typical scenarios include building clinical decision support rules based on drug class (e.g., anticoagulants, statins), normalizing EHR or pharmacy data to RxNorm for analytics, generating medication summaries with class and mechanism explanations, and preparing cohorts for population studies by class membership.

Place the node after any upstream component that selects or extracts a single drug identifier, such as a user input form node, an NLP extraction node reading a medication list, or an integration node receiving pharmacy claims. It often serves as an enrichment source feeding downstream nodes that: (1) filter or route cases based on `drug_classifications` content, (2) expand to all drugs within the same class via nodes like `SaltAIRxClassDrugMembers`, or (3) render clinician-facing explanations using `mechanism_data`. Internally, the node uses RxNorm-related APIs to resolve RXCUIs and call the appropriate classification and mechanism endpoints.

For best results: use `drug_name` when the identifier is a reasonably standardized brand or generic name and `ndc` when you have a verified National Drug Code; constrain `classification_system` to a specific ontology such as `ATC` when you need consistency; disable `include_mechanism` in large batch workflows where mechanism information is unnecessary to reduce external API calls and latency; and always check the `status` output before consuming the returned JSON in downstream logic.

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
<tr><td style="word-wrap: break-word;">drug_identifier</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug you want to analyze, interpreted according to `identifier_type`. For `drug_name`, provide a specific generic or brand name that RxNorm can match (for example, "atorvastatin", "lisinopril"). For `ndc`, supply a valid National Drug Code in standard numeric or dashed format. If the identifier is empty, malformed, or cannot be mapped to an RxNorm concept, the node returns empty JSON objects and an error message in the `status` output.</td><td style="word-wrap: break-word;">atorvastatin</td></tr>
<tr><td style="word-wrap: break-word;">identifier_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines how the node interprets `drug_identifier`. Use `drug_name` when providing a textual drug name recognizable by RxNorm (generic or brand), and `ndc` when providing a National Drug Code. The node chooses the appropriate RxNorm lookup path based on this value. Any value outside `drug_name` or `ndc` will cause RXCUI resolution to fail, leading to empty outputs and a failure status.</td><td style="word-wrap: break-word;">drug_name</td></tr>
<tr><td style="word-wrap: break-word;">classification_system</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The classification source or relationship system to query for drug classes. Valid values come from the RxNorm integration and typically include options such as `ATC`, `MEDRT`, `VA`, `SNOMEDCT`, along with a catch-all like `ALL`. When set to `ALL`, the node retrieves classes from all supported systems; when set to a specific code (for example, `ATC`), it restricts results to that classification system. If the input does not match a supported option, the backend may return fewer or no classes.</td><td style="word-wrap: break-word;">ALL</td></tr>
<tr><td style="word-wrap: break-word;">include_mechanism</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Determines whether mechanism-of-action data should be fetched for the resolved RXCUI. When `true`, the node calls a mechanism-of-action endpoint exposed through the RxNorm-related API and embeds the returned structure into the `mechanism_data` JSON, along with a note that additional databases may be required for comprehensive coverage. When `false`, mechanism lookups are skipped and `mechanism_data` will contain an empty or minimal analysis block, which reduces external calls and improves throughput.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">drug_classifications</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the main classification results. It includes: `drug_identifier` (the original input), `rxcui` (the resolved RxNorm Concept Unique Identifier), `analysis_type` (describing the workflow), `classifications` (an array where each element has `class_id`, `class_name`, `class_type`, and an RxClass `class_url`), and `summary`. The `summary` subsection contains `total_classes`, a list of `classification_systems` found across the classes, and a `therapeutic_categories` list derived from class-name keywords such as "inhibitor", "agonist", or "antagonist". Downstream nodes and applications can parse this JSON to drive analytics, filtering, or decision rules.</td><td style="word-wrap: break-word;">{"drug_identifier": "atorvastatin", "rxcui": "83367", "analysis_type": "Drug Class and Mechanism of Action Analysis", "classifications": [{"class_id": "C10AA05", "class_name": "HMG CoA reductase inhibitors", "class_type": "ATC", "class_url": "https://rxnav.nlm.nih.gov/REST/rxclass/class/C10AA05"}], "summary": {"total_classes": 1, "classification_systems": ["ATC"], "therapeutic_categories": ["HMG CoA reductase inhibitors"]}}</td></tr>
<tr><td style="word-wrap: break-word;">mechanism_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string summarizing mechanism-of-action information for the drug's RXCUI. It contains `rxcui`, `mechanism_analysis` (the raw or lightly processed payload from the mechanism-of-action service), and a `note` explaining that additional pharmacology databases such as DrugBank or MeSH may be needed for complete coverage. If `include_mechanism` is false or the backend provides no mechanism information, `mechanism_analysis` will be empty or minimal, but the structure remains valid JSON.</td><td style="word-wrap: break-word;">{"rxcui": "83367", "mechanism_analysis": {"source": "RxNorm", "mechanisms": [{"description": "Inhibits HMG-CoA reductase, the rate-limiting enzyme in cholesterol synthesis.", "evidence_level": "clinical"}]}, "note": "Mechanism of action data may require integration with additional databases like DrugBank or MeSH"}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable message indicating whether the workflow succeeded or failed. On success it includes the input identifier and resolved RXCUI (for example, "Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)"). On failure it reports which step failed, such as an inability to obtain an RXCUI or an error from the classification API. Downstream logic should always inspect this field before trusting `drug_classifications` or `mechanism_data`.</td><td style="word-wrap: break-word;">Successfully analyzed drug classes for atorvastatin (RXCUI: 83367)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each run performs at least one RxNorm normalization call plus one classification query; enabling `include_mechanism` adds another external call, so disable it when running large-scale batch analyses that do not require mechanism details.
- **Limitations**: The node depends on RxNorm and associated services; unusual local codes, misspelled drug names, non-standard abbreviations, or malformed NDCs may not map to any RXCUI and will result in empty JSON outputs with an error status.
- **Behavior**: The `therapeutic_categories` list in the `summary` is based on simple keyword detection in class names and should be treated as a heuristic label set, not as a curated therapeutic ontology.
- **Behavior**: The `classification_system` input must match one of the systems supported by the RxNorm integration; unexpected values or case differences can lead to missing or incomplete class results even when RXCUI resolution succeeds.
- **Performance**: Outputs are JSON strings; when using them in tight loops, parse once per result and reuse parsed structures rather than repeatedly serializing and deserializing to avoid unnecessary overhead.

## Troubleshooting
- **RXCUI resolution failure**: If `status` includes "Failed to get RxCUI" and both `drug_classifications` and `mechanism_data` are `{}`, verify that `drug_identifier` is non-empty, spelled correctly (for names), or formatted properly (for NDC), and that `identifier_type` correctly reflects the format (`drug_name` vs `ndc`).
- **Classification retrieval error**: When `drug_classifications` contains an `error` field and `status` says "Failed to get classifications", check network connectivity and RxNorm service availability, confirm the RXCUI is valid, and ensure `classification_system` is set to a supported option such as `ALL` or a known code like `ATC`.
- **Missing or empty mechanism data**: If `include_mechanism` is true but `mechanism_analysis` is empty, the backend may lack mechanism-of-action data for that RXCUI or may be partially unavailable; test a well-known drug as a control and consider supplementing mechanism information from external pharmacology databases when necessary.
- **Unexpectedly few classes**: If a valid RXCUI returns very few or no items in `classifications`, try switching `classification_system` from a specific value to `ALL` to broaden the search, and confirm that the chosen ontology actually includes classifications for that drug.
