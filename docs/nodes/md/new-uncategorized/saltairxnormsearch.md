# RxNorm Drug Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches RxNorm for drug concepts by name and returns the full search response, extracted RxCUI identifiers, and a status message. Supports toggling whether to search across all terms or only active terms. Outputs are returned as JSON strings for easy downstream parsing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltairxnormsearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of an RxNorm workflow to look up a medication by its common or brand name. The returned RxCUI list can be passed to downstream nodes that require concept identifiers (for example, to fetch RxTerms info, related concepts, or interactions). Enable the 'search_all_terms' option to broaden results when a strict active-term search does not find a match.

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
<tr><td style="word-wrap: break-word;">drug_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug name to search for in RxNorm. Can be a generic or brand name.</td><td style="word-wrap: break-word;">acetaminophen</td></tr>
<tr><td style="word-wrap: break-word;">search_all_terms</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, searches across all terms; if false, restricts to active terms. Use true to broaden results when needed.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the full search payload, including the original search term, the search mode, and the raw RxNorm API results.</td><td style="word-wrap: break-word;">{   "search_term": "acetaminophen",   "search_all_terms": false,   "results": { "idGroup": { "name": "acetaminophen", "rxnormId": ["161"] } } }</td></tr>
<tr><td style="word-wrap: break-word;">rxcui</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string array of RxCUI identifiers extracted from the search results, suitable for input to other Rx-related nodes.</td><td style="word-wrap: break-word;">["161"]</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message indicating success or describing an error.</td><td style="word-wrap: break-word;">Successfully searched for 'acetaminophen' in RxNorm</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Drug name is required and cannot be empty; an empty input returns an error status.
- The 'rxcui' output is a JSON string representing an array. If you need a list/array object, parse this string in your workflow.
- Searching all terms may return inactive, historical, or source-specific terms; keep this in mind for clinical or production filters.
- Network access to the RxNorm API is required; transient network/API issues will be surfaced in the status message and results payload.
- The search results payload mirrors the RxNorm API structure and may include additional metadata beyond the RxCUI list.

## Troubleshooting
- Error: Drug name cannot be empty: Provide a non-empty drug_name value.
- Status shows 'API Error: ...': Check network connectivity and try again; if persistent, verify the RxNorm service status.
- No RxCUIs returned: Try enabling 'search_all_terms' to broaden the search or refine the drug_name (e.g., remove dosage or form).
- Downstream node cannot parse rxcui: Ensure you parse the 'rxcui' output string as JSON before using it as an array.
