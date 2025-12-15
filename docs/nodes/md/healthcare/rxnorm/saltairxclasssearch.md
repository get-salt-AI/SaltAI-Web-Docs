# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches RxClass by a provided class name and returns the results as a JSON string along with a human-readable status. It validates the input, queries the underlying RxNorm service, and formats the response to include the original search term and results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclasssearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up RxClass entries by a class name (e.g., therapeutic class or mechanism-related classes). Typically placed early in a workflow to identify class IDs or verify available classes before fetching detailed info or members.

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
<tr><td style="word-wrap: break-word;">class_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug class name to search for in RxClass.</td><td style="word-wrap: break-word;">ACE Inhibitors</td></tr>
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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the search term and the raw results returned by the RxClass service.</td><td style="word-wrap: break-word;">{   "search_term": "ACE Inhibitors",   "results": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A status message indicating success or describing any error encountered during the search.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The class_name input must not be empty; an empty value returns an error status and an empty JSON object.
- The first output is a JSON string; downstream nodes or scripts may need to parse it to access fields such as results.
- If the underlying service reports an error, the first output contains the error JSON and the status will start with 'API Error:'.
- Results are formatted to include both the original search_term and the results payload for clarity.
- Network or service rate limits can affect response time and availability. Retry if transient errors occur.

## Troubleshooting
- Empty or blank class name: Provide a non-empty class_name to avoid 'Error: Class name cannot be empty'.
- API Error in status: Inspect the class_search_results JSON for an 'error' field and adjust the query or retry later.
- Parsing issues downstream: Ensure you parse the JSON string from class_search_results before attempting to access fields.
- Unexpected or empty results: Verify the class name spelling or try alternative class terms; some classes may not have entries or may be named differently.
