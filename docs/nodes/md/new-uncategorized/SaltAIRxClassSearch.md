# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxClass database by a drug class name and returns the results as a JSON string along with a status message. It formats the output to include the original search term and the raw results from the RxClass service.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltAIRxClassSearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up drug classification entries by name (e.g., to find class IDs for subsequent queries). Typical workflow: provide a class name (like "ACE Inhibitors"), then pass the returned JSON to downstream steps that extract class identifiers for retrieving detailed info or members.

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
<tr><td style="word-wrap: break-word;">class_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The drug class name to search for within RxClass.</td><td style="word-wrap: break-word;">ACE Inhibitors</td></tr>
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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A pretty-printed JSON string containing the search term and the RxClass search results.</td><td style="word-wrap: break-word;">{   "search_term": "ACE Inhibitors",   "results": { ... } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A status message indicating success or describing the error encountered.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: An empty or whitespace-only class_name returns '{}' and an error status.
- **Output format**: Results are returned as a JSON string. Parse the string to access fields programmatically.
- **Error propagation**: If the underlying service returns an error, the JSON will include the error details and the status will begin with 'API Error:'.
- **Result structure**: The JSON groups data as {"search_term": <input>, "results": <raw_api_response>}. Downstream nodes may need to extract class IDs from the results.

## Troubleshooting
- **Empty input error**: If status says 'Error: Class name cannot be empty', provide a non-empty class_name.
- **No matches found**: If results are empty, try a broader or alternate class name spelling.
- **API Error**: If status starts with 'API Error:', verify the class name and retry later; transient network or service issues may be the cause.
- **Parsing issues**: If a downstream step expects structured data, ensure you parse the class_search_results string into JSON before accessing fields.
