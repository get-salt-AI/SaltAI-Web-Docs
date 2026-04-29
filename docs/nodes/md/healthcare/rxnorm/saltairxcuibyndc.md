# RxCUI by NDC

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes a National Drug Code (NDC) and looks up the corresponding RxNorm Concept Unique Identifier(s) (RxCUIs) via the RxNorm API. It returns a formatted JSON payload with the full API response plus a separate JSON list of RxCUI IDs extracted from the result. Use it to bridge from real-world product codes (NDCs) into standardized RxNorm concepts for downstream clinical and analytic workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxcuibyndc.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you have a National Drug Code (for example, from claims, pharmacy systems, or EHR medication records) and need to convert it into RxNorm concept identifiers. It typically appears early in a medication normalization pipeline: (1) upstream nodes or data connectors provide an NDC string, (2) this node resolves that NDC to one or more RxCUIs, (3) downstream nodes like SaltAIRxNormConceptInfo, SaltAIRxNormRelatedConcepts, SaltAIRxNormDrugInteractions, or SaltAIRxClassByDrug consume the RxCUIs to fetch clinical properties, related concepts, or drug classes. The node integrates with the RxNorm service through an internal RxNormAPI.rxcui_by_ndc call and abstracts away the API details. In practice, you might chain this node from an ETL or import step that parses medication data, then pass its rxcui output directly into concept-level analytics, cohort building, or decision support logic. Prefer using the parsed rxcui list output for programmatic flows, while the rxcui_info JSON is well-suited for storage, logging, or visualization.

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
<tr><td style="word-wrap: break-word;">ndc</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">National Drug Code to resolve to RxNorm concepts. Must be a non-empty string; can be provided as an 11-digit normalized NDC without dashes or a standard NDC format with hyphens, depending on how your data is stored. Leading zeros must be preserved. If the string is blank or whitespace only, the node returns an error status and no lookup is performed.</td><td style="word-wrap: break-word;">00071015527</td></tr>
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
<tr><td style="word-wrap: break-word;">rxcui_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the original NDC and the full RxNorm API response. The structure wraps the raw payload under the key rxcui_data, preserving everything returned by the RxNorm rxcui_by_ndc endpoint. Use this when you need detailed context about the mapping, including idGroup, term types, or other metadata.</td><td style="word-wrap: break-word;">{ "ndc": "00071015527", "rxcui_data": { "idGroup": { "ndc": ["00071015527"], "name": "Lisinopril 10 MG Oral Tablet", "rxnormId": ["314076"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded list of RxNorm RxCUI identifiers extracted from the API result. If RxNorm does not return any IDs, this will be an empty list encoded as a string ("[]"). This output is intended for chaining into other RxNorm-based nodes that operate on lists of identifiers.</td><td style="word-wrap: break-word;">["314076"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable status message describing whether the lookup succeeded or failed. On success, it includes the NDC; on API or validation errors, it contains an error description that you can surface in logs or user interfaces.</td><td style="word-wrap: break-word;">Successfully retrieved RxCUI for NDC 00071015527</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution performs a live call to the RxNorm service via rxcui_by_ndc, so latency depends on network conditions and API responsiveness; batch large NDC lists thoughtfully to avoid excessive sequential calls.
- **Limitations**: If the RxNorm API does not return an idGroup.rxnormId field (for example, for an unmapped or invalid NDC), the rxcui output becomes an empty JSON list encoded as a string ("[]") rather than raising an exception.
- **Behavior**: An empty or whitespace-only NDC string short-circuits processing and returns rxcui_info as "{}", rxcui as an empty string, and a status like "Error: NDC cannot be empty".
- **Behavior**: When the RxNorm service signals an error such as downtime or a bad request, the node serializes the error payload into rxcui_info, leaves rxcui empty, and prefixes the status with "API Error:", which is useful for programmatic error handling.

## Troubleshooting
- **NDC cannot be empty**: If the status is "Error: NDC cannot be empty" and rxcui_info is "{}", ensure that your upstream node or data source is actually passing a non-empty string and that any trimming or formatting logic does not strip the entire value.
- **API Error: ...**: When the status begins with "API Error:" and rxcui is empty, inspect the rxcui_info JSON for the error field from the RxNorm service; common causes include malformed NDCs, temporary service unavailability, or rate limiting. Retry with a validated NDC and consider backing off on repeated failures.
- **Empty rxcui list**: If rxcui is "[]" but there is no explicit error, it means the lookup completed but RxNorm has no RxCUI for that NDC. Verify that the NDC is current, correctly formatted (including leading zeros), and not deprecated or local-only to your institution.
- **JSON parsing downstream**: Downstream nodes typically expect rxcui to be a JSON-encoded list of strings; if you see index-out-of-range or JSON decode errors later in the pipeline, confirm that you are not double-encoding or double-decoding and that the consumer node is treating the field as JSON, not as a raw list.
