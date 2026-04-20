# ClinicalTrials Study Fields

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries the ClinicalTrials.gov stats/field/values API to retrieve information about available study fields and their values. It lets you filter by field type and by a comma-separated list of specific fields, and returns both the raw results and lightweight metadata as JSON strings. It is useful as a discovery and inspection tool to understand what structured fields are available for downstream clinical trial analyses.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/clinicaltrials/clinicaltrialsstudyfieldsnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to explore or programmatically discover which study fields and values are available in ClinicalTrials.gov data before crafting more detailed trial queries. It typically appears early in a clinical-trials workflow, helping you identify relevant fields (such as conditions, interventions, or statuses) that can then be used in nodes that search or analyze specific trials. Upstream, the `type` and `fields` inputs are often driven by user configuration, prompts, or project presets. Downstream, the JSON string outputs are commonly fed into parsing and transformation nodes (for example, JSON parsing, list extraction, or text templating) in order to build UI dropdowns, filter options, analytics reports, or validation checks on field names. In practice, you might first call ClinicalTrialsStudyFieldsNode to retrieve valid `Condition` and `InterventionName` values, then feed selected values into a separate node that queries or aggregates matching studies for clinical research, evidence synthesis, or feasibility analysis.

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
<tr><td style="word-wrap: break-word;">type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter indicating which group or category of fields to retrieve from ClinicalTrials.gov. When set, only fields belonging to this type are returned by the stats/field/values endpoint. The allowed values and their semantics depend on the ClinicalTrials.gov API; unsupported or misspelled values may yield empty or reduced results. Leave this blank to let the API return all applicable field types.</td><td style="word-wrap: break-word;">Condition</td></tr>
<tr><td style="word-wrap: break-word;">fields</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of specific study fields to include in the response. Each entry should be a valid ClinicalTrials.gov field name, separated by commas without surrounding quotes. If omitted or left empty, the API may return a broader set of fields depending on the selected type. Use this to constrain responses to the subset of fields you intend to expose or analyze downstream, improving clarity and reducing payload size.</td><td style="word-wrap: break-word;">Condition,InterventionName,OverallStatus</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string containing the raw field data returned by the ClinicalTrials.gov stats/field/values endpoint. The structure is defined by the external API and typically includes one or more entries per field with associated values and counts. Downstream nodes should treat this as JSON text and parse it before further processing, such as extracting lists of values, building user interface elements, or computing statistics.</td><td style="word-wrap: break-word;">{   "Condition": [     {"field": "Condition", "value": "Breast Cancer", "count": 12456},     {"field": "Condition", "value": "Diabetes Mellitus", "count": 9876}   ],   "InterventionName": [     {"field": "InterventionName", "value": "Trastuzumab", "count": 532},     {"field": "InterventionName", "value": "Metformin", "count": 2140}   ] }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Pretty-printed JSON string with summary metadata about the response. At minimum, this includes a `result_count` field indicating how many top-level records or groups were returned. This is useful for quick validation, logging, and conditional branching (for example, skipping downstream processing if there are zero results).</td><td style="word-wrap: break-word;">{   "result_count": 2 }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes a live HTTP GET request to the ClinicalTrials.gov API with a 30-second timeout; large or unfiltered queries and slow network conditions can increase latency, so consider narrowing the `fields` list where possible.
- **Limitations**: The exact structure, available field names, and semantics of the returned JSON are controlled by the ClinicalTrials.gov API and may evolve over time; avoid hard-coding assumptions about specific keys beyond what you dynamically inspect.
- **Behavior**: If the `type` or `fields` inputs are left empty, they are simply omitted from the query parameters, and the API will apply its default behavior, which might return broader or different sets of data than expected.
- **Behavior**: On network, JSON parsing, or unexpected errors, the node does not raise exceptions to the pipeline but instead returns JSON strings where both `results` and `metadata` contain an `error` key describing the issue; downstream nodes must check for this before using the data.

## Troubleshooting
- **Empty or Very Small Result Set**: Symptom – `results` parses successfully but `metadata.result_count` is 0 or unexpectedly low. Likely cause – the `type` filter is too restrictive or some field names in `fields` are misspelled or not supported. Resolution – temporarily remove `type`, reduce `fields` to a single commonly-used value (such as `Condition`), and re-run to confirm the connection and valid field names.
- **Network Error in Output**: Symptom – both `results` and `metadata` contain a JSON object like `{ "error": "Network error" }`. Likely cause – ClinicalTrials.gov is temporarily unavailable, there is no outbound internet access, or requests are blocked by a firewall or proxy. Resolution – verify general internet connectivity and any corporate proxy configuration, then retry later if the remote service appears to be down.
- **JSON Decode Error in Output**: Symptom – the outputs contain `{ "error": "JSON decode error" }`. Likely cause – the API response is not valid JSON (for example, an HTML error page or truncated data) due to transient service issues. Resolution – wait and retry; if the problem persists, inspect the raw HTTP response using an external tool to verify the API’s health.
- **Downstream Parsing Failures**: Symptom – downstream JSON parsing or field access nodes fail with key errors or type mismatches. Likely cause – assuming a fixed schema for the `results` JSON that does not match the current API response. Resolution – first parse and inspect the top-level keys and shapes (for example, by printing them or using a schema-inspection step) and update downstream logic to handle the actual structure, including potential new or missing fields.
