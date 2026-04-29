# RxTerms Information

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the RxNorm RxTerms API via an internal utility to fetch term-level details for one or more RxCUIs. It accepts either a single RxCUI string or a JSON array of RxCUIs, then returns JSON-encoded results along with separate mappings for full name, strength, route, and dose forms. It handles errors on a per-RxCUI basis by returning empty structures where data is unavailable, so downstream logic can safely continue processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxtermsinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have one or more RxNorm concept identifiers (RxCUIs) and need human-readable drug term details for display, documentation, or reasoning. It is typically used after a lookup node that produces RxCUIs (such as a search over drug names) and before nodes that format or analyze medication data, including LLM prompt builders, reporting nodes, or decision-support logic. A common workflow is: resolve a drug name with a search node like "SaltAIRxNormSearch", optionally refine or expand with relationship-focused nodes like "SaltAIRxNormRelatedConcepts", then send one or more RxCUIs into "SaltAIRxTermsInfo" to obtain consistent full names, strengths, routes, and dose forms. Prefer passing a JSON array of RxCUIs when you have multiple concepts to enrich in a single step; downstream, consume the returned JSON mappings directly or map them into tables, forms, or prompt templates for clinical narratives.

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
<tr><td style="word-wrap: break-word;">rxcui</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">RxNorm Concept Unique Identifier value or values. If you pass a simple string (for example, "313"), the node treats it as a single RxCUI. If the string starts with "[" and ends with "]", it is parsed as a JSON array of RxCUI strings (for example, "[\"313\", \"1049630\"]"). Each RxCUI should be a non-empty string; blank entries are accepted but will yield empty results for that key.</td><td style="word-wrap: break-word;">["313", "1049630"]</td></tr>
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
<tr><td style="word-wrap: break-word;">rxterms_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded object mapping each input RxCUI to the full RxTerms response returned by the service. Keys are RxCUI strings and values are the complete payloads, typically including an `rxtermsProperties` object. For RxCUIs that fail or have no data, the value is an empty object.</td><td style="word-wrap: break-word;">{"313": {"rxtermsProperties": {"rxcui": "313", "fullName": "acetaminophen 325 MG Oral Tablet", "strength": "325 MG", "route": "ORAL", "rxnormDoseForm": "Oral Tablet"}}, "1049630": {"rxtermsProperties": {"rxcui": "1049630", "fullName": "ibuprofen 200 MG Oral Tablet", "strength": "200 MG", "route": "ORAL", "rxnormDoseForm": "Oral Tablet"}}}</td></tr>
<tr><td style="word-wrap: break-word;">full_name</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded mapping from RxCUI to the RxTerms `fullName` field. This is a concise, human-readable drug description that usually includes strength and form. RxCUIs with missing or errored data map to an empty string.</td><td style="word-wrap: break-word;">{"313": "acetaminophen 325 MG Oral Tablet", "1049630": "ibuprofen 200 MG Oral Tablet"}</td></tr>
<tr><td style="word-wrap: break-word;">strength</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded mapping from RxCUI to the `strength` field from RxTerms. This is typically the dose or concentration value such as "325 MG". RxCUIs with no strength information return an empty string.</td><td style="word-wrap: break-word;">{"313": "325 MG", "1049630": "200 MG"}</td></tr>
<tr><td style="word-wrap: break-word;">route</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded mapping from RxCUI to the `route` field from RxTerms, such as ORAL, INTRAVENOUS, or TOPICAL. If route is unavailable or an error occurred, the mapping value is an empty string.</td><td style="word-wrap: break-word;">{"313": "ORAL", "1049630": "ORAL"}</td></tr>
<tr><td style="word-wrap: break-word;">forms</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded mapping from RxCUI to the `rxnormDoseForm` field from RxTerms. This indicates the dose form label such as "Oral Tablet" or "Oral Suspension". Missing or failed lookups yield an empty string for that RxCUI.</td><td style="word-wrap: break-word;">{"313": "Oral Tablet", "1049630": "Oral Tablet"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node loops over each RxCUI and calls the RxTerms endpoint through an internal API utility; large arrays of RxCUIs can increase latency and may run into external service rate limits.
- **Limitations**: Only valid RxNorm concept identifiers are supported. Malformed or non-existent RxCUIs will not raise a detailed error per item; instead, they produce empty outputs for that key, so you must inspect the mappings to detect missing data.
- **Behavior**: Any input string that starts with "[" and ends with "]" is treated as JSON and parsed as an array of RxCUI strings. If parsing fails, the operation can fail before any external call; ensure you provide syntactically valid JSON arrays when using batch mode.
- **Behavior**: For each RxCUI that triggers an error or returns an error structure from the RxTerms service, the node inserts an empty object in `rxterms_info` and empty strings in `full_name`, `strength`, `route`, and `forms`, allowing downstream nodes to continue processing other RxCUIs without failing.

## Troubleshooting
- **Symptom: node fails when passing multiple RXCUIs**: This often happens when the `rxcui` input looks like an array but is not valid JSON (for example, [313, 1049630] without quotes). Correct this by sending a properly quoted JSON string such as "[\"313\", \"1049630\"]".
- **Symptom: some RXCUIs return empty strings while others have data**: Those specific RXCUIs either do not exist in RxTerms or caused an error. Verify the IDs using a search node like "SaltAIRxNormSearch" and ensure they refer to active concepts.
- **Symptom: route, strength, or form is unexpectedly blank**: In some RxTerms records, certain properties may be absent even for valid concepts. Design downstream logic to tolerate empty strings and, if necessary, fall back to the full raw payload from `rxterms_info`.
- **Symptom: latency is high with many RXCUIs**: Large batches cause multiple sequential calls to the RxTerms service. Reduce the batch size per run, cache results for frequently queried RXCUIs upstream, or limit usage to the specific drugs you actually need to display or analyze.
