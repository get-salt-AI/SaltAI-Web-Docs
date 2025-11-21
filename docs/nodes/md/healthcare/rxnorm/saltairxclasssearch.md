# RxClass Search

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the RxClass database by a drug class name and returns formatted JSON results. The node validates input, calls the RxNorm RxClass service via an internal API helper, and provides a human-readable status message.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/healthcare/rxnorm/saltairxclasssearch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to look up RxClass information starting from a class name (e.g., to find matching pharmacologic or therapeutic classes). Typical workflow: provide a class name, retrieve structured search results as JSON, and feed that JSON into downstream nodes for selection, filtering, or further analysis.

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
<tr><td style="word-wrap: break-word;">class_search_results</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the search term and the returned results from RxClass. On success, the payload is formatted as: {"search_term": "<class_name>", "results": <api_results>}. On input or runtime error, an empty JSON object "{}" is returned.</td><td style="word-wrap: break-word;">{   "search_term": "ACE Inhibitors",   "results": {     "rxclassMinConceptList": [       {"classId": "100", "className": "ACE Inhibitors", "classType": "PHARMACOKINETIC"}     ]   } }</td></tr>
<tr><td style="word-wrap: break-word;">status</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable status message describing the outcome, e.g., success, input validation error, API error, or runtime error.</td><td style="word-wrap: break-word;">Successfully searched RxClass for 'ACE Inhibitors'</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validation**: If class_name is empty or whitespace, the node returns "{}" with status "Error: Class name cannot be empty".
- **API error propagation**: If the underlying API returns an error (exposed as an 'error' field in the response), the node returns the API's error JSON and sets status to "API Error: <message>".
- **Output format**: On success, results are wrapped with the original search term and pretty-printed for readability.
- **Data type**: Both outputs are strings; the first is a JSON-encoded string that downstream nodes may need to parse.
- **Service dependency**: Relies on an internal RxNorm API helper to query RxClass; network or service issues will surface as error status with an empty JSON payload.

## Troubleshooting
- **Empty results or '{}' output**: Verify that 'class_name' is non-empty and correctly spelled. The node returns '{}' when input is blank or a runtime error occurs.
- **Status shows 'API Error: ...'**: The RxClass service returned an error. Check the error message within the JSON output for details, adjust the class name, or retry later.
- **Downstream parsing failures**: Ensure the consumer expects a JSON string and parses it before accessing fields. The output is not a native object.
- **Unexpected fields in results**: RxClass responses can vary. Inspect the 'results' section to confirm the presence of expected keys before using them downstream.
