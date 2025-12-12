# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches RxClass by a drug class name and returns a JSON-formatted string of results along with a human-readable status. It wraps the RxNorm RxClass search, validates the input, and consistently structures the response with a search_term and results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclasssearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up drug classes by name to drive downstream workflows like retrieving class details or members. Provide a class name (e.g., ACE Inhibitors) and route the JSON results to nodes that parse or display RxClass data.

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
<tr><td style="word-wrap: break-word;">class_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug class name to search in RxClass. Must be non-empty.</td><td style="word-wrap: break-word;">ACE Inhibitors</td></tr>
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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the structured response with the original search term and the results returned by RxClass.</td><td style="word-wrap: break-word;">{"search_term": "ACE Inhibitors", "results": {"rxclassMinConceptList": {...}}}</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A status message indicating success or describing an error condition.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input must be non-empty**: An empty or whitespace-only class_name returns an empty JSON object and an error status.
- **JSON is returned as a STRING**: The first output is a JSON string. Downstream nodes may need to parse it before accessing fields.
- **Consistent structure**: On success, the JSON includes keys: search_term and results. On API errors, results may include an "error" field and the status will start with "API Error:".
- **External service dependency**: Results depend on the RxNorm/RxClass service availability and the exact spelling of the class name.

## Troubleshooting
- **Empty results or error status**: Ensure class_name is non-empty and correctly spelled. Try alternative or broader class terms.
- **Status shows 'API Error'**: The external API may be unavailable or returned an error. Retry later or adjust the query.
- **Downstream parsing failures**: Remember the first output is a JSON string; parse it before accessing keys like results or rxclassMinConceptList.
- **Inconsistent fields in results**: The RxClass API may return different structures depending on the query. Inspect the raw JSON output to understand available fields.
