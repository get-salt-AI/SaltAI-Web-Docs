# OpenFDA Drug Product Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Queries the OpenFDA Drug Label endpoint to retrieve product labeling data (e.g., warnings, adverse reactions, interactions) based on a selected field and search value. Supports both search and count actions, optional exact matching, and a configurable result limit. Returns JSON-formatted strings for both results and API metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/openfda/openfdadproductlabelnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need structured information from FDA drug labeling, such as warnings, precautions, adverse reactions, or instructions for use. Specify the field you want to query, provide a search term, choose whether to search or count, and set the result limit. Integrate it early in workflows that need authoritative drug label text for downstream analysis, summarization, or compliance checks.

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
<tr><td style="word-wrap: break-word;">field_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value to match against the selected field. This is the user-provided query string used in the OpenFDA search.</td><td style="word-wrap: break-word;">aspirin</td></tr>
<tr><td style="word-wrap: break-word;">field</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The label section or attribute to search in. Determines which OpenFDA field is queried.</td><td style="word-wrap: break-word;">adverse reactions</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The operation to perform. 'search' returns matching documents; 'count' returns aggregation counts for the chosen field.</td><td style="word-wrap: break-word;">search</td></tr>
<tr><td style="word-wrap: break-word;">exact_match</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, performs an exact match query on the field using the '.exact' subfield.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of results to return. Must be 1 or greater.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'results' array. For 'search', this is a list of matching label documents; for 'count', this is a list of count buckets.</td><td style="word-wrap: break-word;">[{...}, {...}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the OpenFDA API 'meta' object, including paging and query information.</td><td style="word-wrap: break-word;">{"disclaimer": "...", "results": {"limit": 3, "skip": 0, "total": 1234}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Search Fields**: Available choices include 'abuse', 'adverse reactions', 'components', 'drug interactions', 'effective time', 'general_precautions', 'instructions for use', 'nursing_mothers', 'overdosage', 'precautions', and 'warnings'.
- **Exact Match**: When exact_match is true, the node queries the '.exact' variant of the selected field for precise matches.
- **Actions**: 'search' returns documents; 'count' returns aggregated counts for the selected field value.
- **Output Format**: Both outputs are JSON strings. Downstream nodes may need to parse them before further processing.
- **Limits and Quotas**: OpenFDA enforces rate limits; large limits or frequent calls may hit throttling.
- **Endpoint Used**: Queries https://api.fda.gov/drug/label.json with the constructed parameters.

## Troubleshooting
- **Empty results**: Try broadening 'field_value', switch off exact_match, or increase 'limit'. Verify the chosen field is appropriate for the data you expect.
- **Invalid field errors**: Ensure 'field' is selected from the provided choices. Custom values are not supported.
- **Network error**: Intermittent connectivity or API downtime will return an error JSON string. Retry later or check network settings.
- **JSON decode error**: The API response may be malformed or truncated. Retry the request; if persistent, reduce the 'limit' and check rate limits.
- **Unexpected 'count' output**: When action is 'count', 'results' is an array of buckets rather than documents. Ensure downstream parsing matches the chosen action.
