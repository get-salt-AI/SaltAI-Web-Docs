# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes a National Drug Code (NDC) string and queries an RxNorm service to find the corresponding RxCUI identifiers. It returns a JSON payload with the original NDC and full RxNorm response, a JSON list of extracted RxCUI IDs, and a human-readable status message. It centralizes NDC-to-RxCUI resolution so downstream nodes can operate on standardized RxNorm identifiers instead of raw NDCs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you have an NDC and need to convert it into RxNorm RxCUI identifiers as part of terminology normalization, clinical analytics, or medication data integration. It typically appears early in a medication processing workflow: raw NDCs are ingested from claims, EHR medication lists, or pharmacy systems, passed into this node, and the resulting RxCUIs are then consumed by RxNorm-based analysis nodes (for example, to fetch concept properties or related drugs). Practical scenarios include building cohorts based on drugs dispensed via their NDCs but defined via RxCUIs, harmonizing multiple NDC tables into a single RxNorm-backed drug catalog, or preparing data for drug class and interaction analysis.

In a typical pipeline, you might: (1) clean and normalize incoming NDC strings, (2) send each NDC into "RxCUI by NDC", (3) collect the rxcui output into a list of IDs, and (4) pass those IDs to nodes such as "SaltAIRxNormConceptInfo" or "SaltAIRxNormRelatedConcepts". Always inspect the status field and the rxcui list to handle missing mappings or API errors gracefully. For batch operations, iterate over a list of NDCs and aggregate the outputs rather than expecting this node to handle arrays directly.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Single National Drug Code to resolve to RxCUI. Must be a non-empty string. The underlying RxNorm service typically expects a normalized 11-digit NDC with no separators, though it may accept other normalized formats depending on configuration. If this value is empty or contains only whitespace, the node will not call the external service and will return an error status with no RxCUI IDs.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the original NDC and the full RxNorm response from the NDC lookup. The structure is of the form: {"ndc": "<ndc>", "rxcui_data": <raw_rxnorm_response_object>}. Use this output when you need the complete response, including fields such as idGroup, names, and any additional metadata supplied by the RxNorm API.</td><td style="word-wrap: break-word;">{   "ndc": "00071015527",   "rxcui_data": {     "idGroup": {       "ndc": ["00071015527"],       "name": "amoxicillin 500 MG Oral Capsule",       "rxnormId": ["723"]     }   } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of RxCUI identifiers extracted from the RxNorm response. When the service returns IDs in idGroup.rxnormId, they are emitted here as a JSON array string. If no RxCUIs are present in the response or if an error occurs, this will be an empty JSON array string or an empty string, depending on the error path. Downstream nodes that require RxCUIs should parse this value and select one or more IDs as appropriate.</td><td style="word-wrap: break-word;">["723"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing the outcome of the lookup. On success, it confirms that an RxCUI was retrieved for the given NDC (for example, "Successfully retrieved RxCUI for NDC 00071015527"). On failure, it reports validation problems (such as an empty NDC) or service errors (prefixed with "API Error:"). Use this field for logging and to implement conditional branching in workflows.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution issues a request to an external RxNorm API. When resolving many NDCs, iterate with care and consider batching or inserting small delays to avoid network congestion or exceeding service rate limits.
- **Limitations**: If the supplied NDC is not recognized by RxNorm (for example, due to being obsolete, local-only, or incorrectly formatted), the response may not contain idGroup.rxnormId. In such cases the node produces an empty RxCUI list without raising a hard exception, so downstream logic must verify outputs before relying on them.
- **Behavior**: When the ndc input is empty or only whitespace, the node returns "{}" for rxcui_info, an empty string for rxcui, and a status message beginning with "Error: NDC cannot be empty". No external API call is made in this scenario, which helps avoid unnecessary requests caused by bad upstream data.
- **Behavior**: If the RxNorm API returns an error object, the node wraps that error inside rxcui_info (often under an "error" key), sets rxcui to an empty string, and emits a status message starting with "API Error:". Treat this as a non-recoverable lookup for that NDC unless your workflow includes retry logic.

## Troubleshooting
- **Empty RxCUI list with apparently valid input**: When rxcui is "[]" but rxcui_info shows a structured response, open rxcui_info and inspect idGroup.rxnormId. If it is missing or empty, the NDC has no mapping in the underlying RxNorm service. Verify that the NDC is correctly normalized (for example, 11-digit string without dashes) and confirm that it is a real, active code in your source system.
- **Status shows 'Error: NDC cannot be empty'**: This means the ndc input was blank or contained only whitespace. Fix upstream data preparation by trimming NDC values, filtering out missing codes, or supplying only validated NDC strings before calling this node.
- **Status shows 'API Error: ...'**: A status beginning with "API Error:" indicates a failure at the external service layer (such as connectivity, timeouts, or unexpected responses). Check your network connectivity, any gateway or proxy configurations, and the health of the RxNorm endpoint. Consider retrying with backoff or implementing fallback behavior for affected records.
- **Downstream node fails when parsing outputs**: If a downstream node expects a certain structure in rxcui_info or a non-empty rxcui list, first check the status field. On error, rxcui_info may contain only an error payload and rxcui may be empty. Add guards so downstream nodes run only when status indicates successful retrieval and rxcui contains at least one ID.
