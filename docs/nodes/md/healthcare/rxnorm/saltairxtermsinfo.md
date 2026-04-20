# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the RxTerms service via the internal RxNormAPI to retrieve detailed clinical display information for one or more RxNorm concepts (RXCUIs). It returns the full raw RxTerms response as JSON plus convenient lookup maps for full name, strength, route, and dose form keyed by RXCUI. It is designed to support medication-centric workflows where normalized drug identifiers must be translated into human-readable prescribing information.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxtermsinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have one or more RxNorm concept identifiers (RXCUIs) and need user-facing medication details such as the full drug name, strength, route of administration, and dose form. It typically appears downstream from nodes that resolve drug text or codes to RXCUIs (for example, a node that searches RxNorm by drug name, NDC, or clinical drug concept) and upstream from nodes that generate patient summaries, e-prescription content, or decision-support prompts.

You can pass a single RXCUI as a plain string (e.g. "313") or multiple RXCUIs encoded as a JSON array string (e.g. "[\"313\", \"617314\"]"). The node will call the RxNorm/RxTerms service for each RXCUI and build dictionaries mapping each RXCUI to its full response and key display properties. Downstream nodes can then look up the attributes for the RXCUIs they care about.

Common patterns include: (1) using an upstream node like `SaltAIRxNormSearch` (or similar RxNorm lookup nodes in the same category) to obtain RXCUIs from free-text drug names, then feeding the RXCUIs into `SaltAIRxTermsInfo` to obtain structured details; (2) building patient medication lists by taking a list of RXCUIs from an EHR integration node and enriching them with readable names and strengths for display or natural-language generation; (3) using the `full_name` and `strength` outputs to drive downstream clinical reasoning or dosage-checking nodes. Place this node in the RxNorm/RxTerms section of your pipeline whenever you need to translate normalized drug IDs into human-usable attributes while staying consistent with standardized vocabularies.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">One or more RxNorm Concept Unique Identifiers (RXCUIs). Accepts either a single RXCUI as a plain string (e.g. "313") or a JSON-encoded list of RXCUIs (e.g. "[\"313\", \"617314\"]"). Each RXCUI should be a non-empty string consisting of digits. Empty strings will yield empty results for that entry. The RXCUIs must be valid RxNorm identifiers for the RxTerms API to return data.</td><td style="word-wrap: break-word;">["313", "617314", "197361"]</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string encoding a dictionary that maps each input RXCUI to its full RxTerms response object. For each RXCUI key, the value is whatever the RxNormAPI.get_rxterms call returned (typically an object containing an `rxtermsProperties` block with attributes like fullName, strength, route, rxnormDoseForm, etc.). If an error occurs for a given RXCUI, that RXCUI will map to an empty object. Downstream nodes should parse this JSON to access additional properties beyond the convenience outputs.</td><td style="word-wrap: break-word;">{   "313": {     "rxcui": "313",     "rxtermsProperties": {       "fullName": "amoxicillin 250 MG Oral Capsule",       "strength": "250 mg",       "route": "ORAL",       "rxnormDoseForm": "Capsule"     }   },   "617314": {     "rxcui": "617314",     "rxtermsProperties": {       "fullName": "metformin 500 MG Oral Tablet",       "strength": "500 mg",       "route": "ORAL",       "rxnormDoseForm": "Tablet"     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string encoding a dictionary that maps each RXCUI to its RxTerms `fullName` value. This is a human-readable, clinically oriented name for the drug concept suitable for display in user interfaces, patient summaries, or prompts. For RXCUIs that fail lookup or error, the value will be an empty string.</td><td style="word-wrap: break-word;">{   "313": "amoxicillin 250 MG Oral Capsule",   "617314": "metformin 500 MG Oral Tablet" }</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string encoding a dictionary mapping each RXCUI to its strength as reported by RxTerms (e.g. "250 mg", "10 mg/mL"). This is generally the dose strength per unit. RXCUIs with errors or missing data will map to an empty string.</td><td style="word-wrap: break-word;">{   "313": "250 mg",   "617314": "500 mg" }</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string encoding a dictionary mapping each RXCUI to its route of administration as returned by RxTerms (e.g. "ORAL", "INTRAVENOUS"). Routes are typically uppercase strings aligned with RxNorm / RxTerms conventions. RXCUIs that could not be resolved will map to an empty string.</td><td style="word-wrap: break-word;">{   "313": "ORAL",   "617314": "ORAL" }</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string encoding a dictionary mapping each RXCUI to its dose form from the `rxnormDoseForm` property (e.g. "Tablet", "Capsule", "Injection"). This is useful for differentiating between formulations of the same active ingredient. RXCUIs that encounter errors or lack data will have an empty string value.</td><td style="word-wrap: break-word;">{   "313": "Capsule",   "617314": "Tablet" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node may perform one external RxTerms API call per RXCUI; large lists of RXCUIs can increase latency and may be constrained by upstream RxNorm service throughput.
- **Limitations**: If an RXCUI is invalid, empty, or the RxTerms API returns an error, that RXCUI will still appear in the output dictionaries but with an empty object in `rxterms_info` and empty strings in `full_name`, `strength`, `route`, and `forms`.
- **Behavior**: The `rxcui` input is parsed as JSON only if it starts with '[' and ends with ']'; otherwise it is treated as a single RXCUI string, so ensure brackets and quoting are correct when passing lists.
- **Behavior**: All outputs are STRING-typed JSON dictionaries keyed by RXCUI; downstream nodes must JSON-decode these strings before treating them as structured maps.
- **Integration**: The node depends on network access to the RxNorm/RxTerms service via the internal RxNormAPI; outages or connectivity issues at that service will propagate as missing or empty data for affected RXCUIs.

## Troubleshooting
- **Empty values for a valid-looking RXCUI**: Symptom: `full_name`, `strength`, `route`, and `forms` show empty strings for a particular RXCUI. Check that the RXCUI is a current RxNorm identifier and that there were no connectivity issues. Also ensure there is no leading/trailing whitespace in the ID; trim the input or sanitize it upstream.
- **No results when passing multiple RXCUIs**: Symptom: All outputs are empty or only one RXCUI appears. Verify that the `rxcui` input is a valid JSON array string (e.g. "[\"313\", \"617314\"]"). If you pass an unquoted list or a Python-style list, it will not parse and will be treated as a single string instead.
- **JSON parsing errors downstream**: Symptom: Downstream nodes fail when consuming outputs. Remember that all outputs are JSON-encoded dictionaries, not raw objects. Parse the string as JSON in the downstream node or step before indexing into it.
- **Intermittent missing data from RxTerms**: Symptom: Some RXCUIs intermittently return empty results. This can be due to transient RxNorm service issues. Implement retry logic upstream, or re-run the workflow for affected RXCUIs and consider caching enriched RxTerms data in your own data store.
